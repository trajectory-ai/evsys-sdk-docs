# SFT (/docs/evsys_sdk/algorithms/sft/SFT)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'sft'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;SFTConfig&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;_check_inputs&#x22;" type="&#x22;(self, ctx) -> None&#x22;">
  <PySourceCode>
    ```python
    def _check_inputs(self, ctx: RunContext) -> None:
        if not ctx.extras.get("train_rows"):
            raise RuntimeError("SFT.train: ctx.extras['train_rows'] missing/empty")
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;RunContext&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;setup&#x22;" type="&#x22;(self, ctx, backend) -> None&#x22;">
  <PySourceCode>
    ```python
    async def setup(self, ctx: RunContext, backend: TinkerBackend) -> None:
        rows = ctx.extras["train_rows"]

        # Standardize raw rows → typed ChatMessagesRow (strict), then tokenize
        # → Datum with assistant-span loss masks. The supervise decision is
        # the algorithm's, not the dataset's.
        chat_rows = cast("list[ChatMessagesRow]", parse_rows(rows, TargetFormat.CHAT_MESSAGES))
        self._datums = sft_tokenize(
            chat_rows, backend.get_tokenizer(),
            max_seq_len=self.cfg.max_seq_len,
            enable_thinking=self.cfg.enable_thinking,
            supervise=self.cfg.supervise,
        )
        if not self._datums:
            raise RuntimeError("SFT.setup: tokenization produced no datums")
        self._n_rows = len(rows)
        self._steps_per_epoch = max(1, self._n_rows // self.cfg.batch_size)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;RunContext&#x22;" value="null" />

    <PyParameter name="&#x22;backend&#x22;" type="&#x22;TinkerBackend&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;build_batch&#x22;" type="&#x22;(self, step_idx) -> TrainingBatch&#x22;">
  Slice `batch_size` Datums for `step_idx`, wrapping the dataset
  when the slice straddles the end.

  <PySourceCode>
    ```python
    async def build_batch(self, step_idx: int) -> TrainingBatch:
        """Slice ``batch_size`` Datums for ``step_idx``, wrapping the dataset
        when the slice straddles the end."""
        n = len(self._datums)
        start = (step_idx * self.cfg.batch_size) % n
        end = start + self.cfg.batch_size
        if end <= n:
            data = self._datums[start:end]
        else:
            data = self._datums[start:] + self._datums[: end - n]
        return TrainingBatch(data=data, loss_fn="cross_entropy")
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;step_idx&#x22;" type="&#x22;int&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.training.loop.TrainingBatch&#x22;" />
</PyFunction>

<PyFunction name="&#x22;step_metrics&#x22;" type="&#x22;(self, step_idx, batch, fb_result) -> dict[str, float]&#x22;">
  `train_mean_nll` from the per-position logprobs of each Datum,
  weighted by the loss mask.

  Tinker's cross\_entropy loss returns `loss_fn_outputs[i]["logprobs"]`:
  a per-position vector of log-probabilities of the target token (a
  "perfect" prediction has logprob 0; otherwise negative). The mean NLL
  is `-sum(logprob * weight) / sum(weight)` over the loss-mask
  positions, averaged across the batch.

  <PySourceCode>
    ```python
    def step_metrics(
        self, step_idx: int, batch: TrainingBatch, fb_result: Any,
    ) -> dict[str, float]:
        """``train_mean_nll`` from the per-position logprobs of each Datum,
        weighted by the loss mask.

        Tinker's cross_entropy loss returns ``loss_fn_outputs[i]["logprobs"]``:
        a per-position vector of log-probabilities of the target token (a
        "perfect" prediction has logprob 0; otherwise negative). The mean NLL
        is ``-sum(logprob * weight) / sum(weight)`` over the loss-mask
        positions, averaged across the batch."""
        outputs = getattr(fb_result, "loss_fn_outputs", None)
        if not outputs:
            return {}

        total_logprob = 0.0
        total_weight = 0.0
        for datum, out in zip(batch.data, outputs):
            logprobs = coerce_floats(out.get("logprobs") if isinstance(out, dict)
                                     else getattr(out, "logprobs", None))
            if logprobs is None:
                continue
            weights = coerce_floats(extract_weights(datum))
            if weights is None or len(weights) == 0:
                continue
            # Truncate to min length so a token-count mismatch between the
            # per-position logprobs and the per-position mask doesn't blow up.
            k = min(len(logprobs), len(weights))
            for j in range(k):
                total_logprob += logprobs[j] * weights[j]
                total_weight += weights[j]

        if total_weight <= 0:
            return {}
        return {"train_mean_nll": -float(total_logprob) / float(total_weight)}
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;step_idx&#x22;" type="&#x22;int&#x22;" value="null" />

    <PyParameter name="&#x22;batch&#x22;" type="&#x22;TrainingBatch&#x22;" value="null" />

    <PyParameter name="&#x22;fb_result&#x22;" type="&#x22;Any&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;dict[str, float]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_hyperparams_extra&#x22;" type="&#x22;(self) -> dict[str, Any]&#x22;">
  <PySourceCode>
    ```python
    def _hyperparams_extra(self) -> dict[str, Any]:
        return {"n_train_rows": self._n_rows, "n_train_datums": len(self._datums)}
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;dict[str, typing.Any]&#x22;" />
</PyFunction>
