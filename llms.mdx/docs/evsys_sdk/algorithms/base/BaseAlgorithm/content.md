# BaseAlgorithm (/docs/evsys_sdk/algorithms/base/BaseAlgorithm)



Template-method base for the SDK's training algorithms. See module
docstring.

Subclasses set the `name` / `Config` ClassVars (the registry contract)
and override :meth:`setup` + :meth:`build_batch` (+ optionally
:meth:`step_metrics`).

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="null" />

<PyAttribute name="&#x22;cfg&#x22;" type="null" value="&#x22;self.Config.model_validate(kwargs)&#x22;" />

<PyAttribute name="&#x22;steps_per_epoch&#x22;" type="&#x22;int&#x22;" value="null" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, **kwargs) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, **kwargs: Any) -> None:
        self.cfg = self.Config.model_validate(kwargs)
        # Set by setup(); read by _resolve_total_steps() and the loop.
        self._steps_per_epoch: int = 1
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;kwargs&#x22;" type="&#x22;Any&#x22;" value="&#x22;{}&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;setup&#x22;" type="&#x22;(self, ctx, backend) -> None&#x22;">
  One-time prep before the loop. Must set `self._steps_per_epoch`.
  Override in the concrete algorithm.

  <PySourceCode>
    ```python
    async def setup(self, ctx: RunContext, backend: TinkerBackend) -> None:
        """One-time prep before the loop. Must set ``self._steps_per_epoch``.
        Override in the concrete algorithm."""
        raise NotImplementedError
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
  Produce the batch for `step_idx` (0-based). Override.

  <PySourceCode>
    ```python
    async def build_batch(self, step_idx: int) -> TrainingBatch:
        """Produce the batch for ``step_idx`` (0-based). Override."""
        raise NotImplementedError
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;step_idx&#x22;" type="&#x22;int&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.training.loop.TrainingBatch&#x22;" />
</PyFunction>

<PyFunction name="&#x22;step_metrics&#x22;" type="&#x22;(self, step_idx, batch, fb_result) -> dict[str, float]&#x22;">
  Per-step metrics from the forward-backward result. Default no-op.

  <PySourceCode>
    ```python
    def step_metrics(
        self, step_idx: int, batch: TrainingBatch, fb_result: Any,
    ) -> dict[str, float]:
        """Per-step metrics from the forward-backward result. Default no-op."""
        return {}
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

<PyFunction name="&#x22;train&#x22;" type="&#x22;(self, ctx) -> RunResult&#x22;">
  <PySourceCode>
    ```python
    def train(self, ctx: RunContext) -> RunResult:
        if ctx.backend.name != "tinker":
            raise RuntimeError(
                f"{type(self).__name__} requires backend=tinker "
                f"(got '{ctx.backend.name}')."
            )
        return asyncio.run(self._train_async(ctx))
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;RunContext&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.protocols.RunResult&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_train_async&#x22;" type="&#x22;(self, ctx) -> RunResult&#x22;">
  <PySourceCode>
    ```python
    async def _train_async(self, ctx: RunContext) -> RunResult:
        # Validate inputs BEFORE allocating the (real, costly) backend so a
        # missing dataset / model_name fails fast without spinning up tinker.
        self._check_inputs(ctx)

        handles = ctx.extras.get("backend_handles", {})
        model_name = handles.get("model_name") or ctx.extras.get("model_name")
        if not model_name:
            raise RuntimeError("model_name not set in backend handles")
        # Stash so setup() can reach it (e.g. SDFT builds a teacher client over
        # the same base model).
        self._model_name = model_name

        # 1. backend (async factory; allocates the LoRA training client)
        backend = await TinkerBackend.create(
            model_name=model_name,
            lora_rank=self.cfg.lora_rank,
            renderer_name=self.cfg.renderer_name or handles.get("renderer_name"),
            resume_state_path=handles.get("load_checkpoint_path"),
            init_weights_path=handles.get("init_from_checkpoint"),
        )

        # 2. per-algorithm prep (sets self._steps_per_epoch + stashes state)
        await self.setup(ctx, backend)

        # 3. total step count + save cadence
        total_steps = self._resolve_total_steps()
        save_every = self._resolve_save_every(total_steps)

        # 4. log hyperparams once so the experiment record carries them
        ctx.log_store.log_hyperparams({
            "algorithm": self.name,
            **self.cfg.model_dump(),
            "model_name": model_name,
            "total_steps": total_steps,
            "save_every": save_every,
            **self._hyperparams_extra(),
        })

        # 5. compose the loop (self IS the StepBuilder) and run
        evaluators = build_in_loop_evaluators(
            ctx.config.metadata if hasattr(ctx, "config") else None,
            tokenizer=backend.get_tokenizer(),
            store=getattr(ctx, "store", None) or ctx.extras.get("store"),
            model_name=model_name,
            workspace_dir=Path(ctx.output_dir) / "harbor_val",
            run_id=ctx.extras.get("dashboard_run_id"),
        )
        loop = TrainingLoop(
            backend=backend,
            step_builder=self,
            log_store=ctx.log_store,
            output_dir=Path(ctx.output_dir),
            adam_params=tinker.AdamParams(
                learning_rate=self.cfg.learning_rate,
                beta1=self.cfg.adam_beta1,
                beta2=self.cfg.adam_beta2,
                eps=self.cfg.adam_eps,
            ),
            save_every=save_every,
            evaluators=evaluators,
            # Algorithm's own loop-only callbacks (e.g. early_stopping) PLUS the
            # experiment's shared logger instances threaded down via extras, so
            # one logger sees both the loop-scope and experiment-scope hooks.
            callbacks=build_callbacks(self.cfg.callbacks)
            + list(ctx.extras.get("callbacks") or []),
            log_context=ctx.extras.get("log_context"),
        )
        artifacts = await loop.run(num_steps=total_steps)

        # 6. record run_dir + per-checkpoint sampler URIs so downstream
        # consumers (TinkerInference.from_run_result, Experiment._eval_arm)
        # keep working unchanged.
        artifacts_dict = artifacts.as_dict()
        for key, value in artifacts_dict.items():
            ctx.log_store.log_artifact(key, value, kind="checkpoint")

        return RunResult(
            run_id=ctx.run_id,
            status="completed",
            metrics={},
            artifacts=artifacts_dict,
        )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;RunContext&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.protocols.RunResult&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_check_inputs&#x22;" type="&#x22;(self, ctx) -> None&#x22;">
  Validate `ctx.extras` before the backend is allocated. Override to
  fail fast on missing / empty inputs (e.g. no train\_rows). Default
  no-op.

  <PySourceCode>
    ```python
    def _check_inputs(self, ctx: RunContext) -> None:
        """Validate ``ctx.extras`` before the backend is allocated. Override to
        fail fast on missing / empty inputs (e.g. no train_rows). Default
        no-op."""
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;RunContext&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_hyperparams_extra&#x22;" type="&#x22;(self) -> dict[str, Any]&#x22;">
  Extra fields merged into the logged hyperparams. Override to add
  algorithm-specific counts (e.g. `n_train_rows`).

  <PySourceCode>
    ```python
    def _hyperparams_extra(self) -> dict[str, Any]:
        """Extra fields merged into the logged hyperparams. Override to add
        algorithm-specific counts (e.g. ``n_train_rows``)."""
        return {}
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;dict[str, typing.Any]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_resolve_total_steps&#x22;" type="&#x22;(self) -> int&#x22;">
  <PySourceCode>
    ```python
    def _resolve_total_steps(self) -> int:
        if self.cfg.max_steps is not None:
            return self.cfg.max_steps
        return self._steps_per_epoch * self.cfg.num_epochs
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;int&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_resolve_save_every&#x22;" type="&#x22;(self, total_steps) -> int&#x22;">
  Resolve the checkpoint cadence from `save_every` /
  `save_at_fractions`. Identical heuristic across all algos: take the
  GCD of the requested fraction-marks, but fall back to `total/10` if
  that GCD would pathologically save every other step.

  <PySourceCode>
    ```python
    def _resolve_save_every(self, total_steps: int) -> int:
        """Resolve the checkpoint cadence from ``save_every`` /
        ``save_at_fractions``. Identical heuristic across all algos: take the
        GCD of the requested fraction-marks, but fall back to ``total/10`` if
        that GCD would pathologically save every other step."""
        if self.cfg.save_every:
            return self.cfg.save_every
        marks = sorted({
            max(1, int(round(f * total_steps)))
            for f in self.cfg.save_at_fractions
        })
        if not marks:
            return total_steps
        gcd = marks[0]
        for m in marks[1:]:
            gcd = math.gcd(gcd, m)
        min_acceptable = max(1, total_steps // 20)
        if gcd >= min_acceptable:
            return gcd
        return max(1, total_steps // 10)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;total_steps&#x22;" type="&#x22;int&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;int&#x22;" />
</PyFunction>
