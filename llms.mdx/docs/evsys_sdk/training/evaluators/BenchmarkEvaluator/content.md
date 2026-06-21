# BenchmarkEvaluator (/docs/evsys_sdk/training/evaluators/BenchmarkEvaluator)



Score a :class:`~evsys_sdk.Benchmark` against the live sampler.

The :class:`~evsys_sdk.training.loop.TrainingLoop` checks
`run_every` per evaluator (see :meth:`TrainingLoop._is_due`);
a value of `0` disables the evaluator (it never fires).

`chat_template` mirrors the post-training eval spec
(`system_prompt` + `user_template` + `enable_thinking`) so the
same YAML knob configures both the in-loop val and the final test set.

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

<PyAttribute name="&#x22;benchmark&#x22;" type="&#x22;Benchmark&#x22;" value="null" />

<PyAttribute name="&#x22;tokenizer&#x22;" type="&#x22;Any&#x22;" value="null" />

<PyAttribute name="&#x22;run_every&#x22;" type="&#x22;int&#x22;" value="&#x22;0&#x22;" />

<PyAttribute name="&#x22;max_tokens&#x22;" type="&#x22;int&#x22;" value="&#x22;256&#x22;" />

<PyAttribute name="&#x22;temperature&#x22;" type="&#x22;float&#x22;" value="&#x22;0.0&#x22;" />

<PyAttribute name="&#x22;breakdown_keys&#x22;" type="&#x22;list[str]&#x22;" value="&#x22;field(default_factory=list)&#x22;" />

<PyAttribute name="&#x22;metrics&#x22;" type="&#x22;list[str]&#x22;" value="&#x22;field(default_factory=list)&#x22;">
  Registered metric names to compute (harbor engine), e.g.
  `["pass@3", "pass^3", "avg"]`. Empty → `mean_reward` + `pass_rate`.
</PyAttribute>

<PyAttribute name="&#x22;chat_template&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;field(default_factory=dict)&#x22;" />

<PyAttribute name="&#x22;limit&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;">
  Cap the number of tasks scored per eval — useful when the benchmark
  is large and you want quick in-loop snapshots.
</PyAttribute>

<PyAttribute name="&#x22;engine&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;">
  `"harbor"` → score through harbor's rollout engine (off the eval
  checkpoint). Anything else → the live-sampler InferenceClient path.
</PyAttribute>

<PyAttribute name="&#x22;model_name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;workspace_dir&#x22;" type="&#x22;Any&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;num_samples&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />

<PyAttribute name="&#x22;n_concurrent&#x22;" type="&#x22;int&#x22;" value="&#x22;8&#x22;">
  Concurrent harbor trials (harbor engine only). Higher = more rollouts in
  flight against the sampler; all share one cached sampling client.
</PyAttribute>

<PyAttribute name="&#x22;store&#x22;" type="&#x22;Any&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;run_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;benchmark_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;evaluate&#x22;" type="&#x22;(self, sampler, *, model_path=None, step=None) -> dict[str, float]&#x22;">
  <PySourceCode>
    ```python
    async def evaluate(
        self, sampler: Any, *,
        model_path: str | None = None, step: int | None = None,
    ) -> dict[str, float]:
        if self.engine.lower() == "harbor" and model_path and self.model_name:
            return await self._evaluate_harbor(model_path, step=step)
        loop = asyncio.get_running_loop()
        client: Any = _AsyncToSyncSampler(sampler, self.tokenizer, loop)
        if self.chat_template:
            client = ChatTemplatedInference(client, **self.chat_template)
        score = await asyncio.to_thread(
            self.benchmark.score,
            client,
            max_tokens=self.max_tokens,
            temperature=self.temperature,
            breakdown_keys=list(self.breakdown_keys),
            limit=self.limit,
            metrics=list(self.metrics),
            num_samples=self.num_samples,
        )
        return dict(score.metrics)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;sampler&#x22;" type="&#x22;Any&#x22;" value="null" />

    <PyParameter name="&#x22;model_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict[str, float]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_evaluate_harbor&#x22;" type="&#x22;(self, model_path, *, step=None) -> dict[str, float]&#x22;">
  Score the validation benchmark through harbor (same engine as
  training); reward = each task's verifier. Returns the metric dict and,
  when `store` + `run_id` are set, uploads the eval rollouts.

  <PySourceCode>
    ```python
    async def _evaluate_harbor(
        self, model_path: str, *, step: int | None = None,
    ) -> dict[str, float]:
        """Score the validation benchmark through harbor (same engine as
        training); reward = each task's verifier. Returns the metric dict and,
        when ``store`` + ``run_id`` are set, uploads the eval rollouts."""
        import tempfile
        from pathlib import Path

        ws = Path(self.workspace_dir) if self.workspace_dir else Path(
            tempfile.mkdtemp(prefix="evsys_val_")
        )
        if step is not None:
            ws = ws / f"step_{step}"
        score = await self.benchmark.score_via_harbor(
            model_name=self.model_name,
            model_path=model_path,
            workspace_dir=ws,
            num_samples=self.num_samples,
            max_tokens=self.max_tokens,
            temperature=self.temperature,
            system_prompt=(self.chat_template or {}).get("system_prompt"),
            limit=self.limit,
            breakdown_keys=list(self.breakdown_keys),
            metrics=list(self.metrics) or None,
            n_concurrent=self.n_concurrent,
        )
        if self.store is not None and self.run_id:
            tasks = (self.benchmark.tasks if self.limit is None
                     else self.benchmark.tasks[: max(0, self.limit)])
            self._upload(tasks, score.rollouts, dict(score.metrics), step)
        return dict(score.metrics)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;model_path&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict[str, float]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_upload&#x22;" type="&#x22;(self, tasks, groups, metrics, step) -> None&#x22;">
  Record one `eval` (per step) + its per-task rollout predictions on
  the dashboard. Best-effort: a dashboard hiccup must not kill training.

  <PySourceCode>
    ```python
    def _upload(
        self, tasks: list[Any], groups: list[Any],
        metrics: dict[str, float], step: int | None,
    ) -> None:
        """Record one ``eval`` (per step) + its per-task rollout predictions on
        the dashboard. Best-effort: a dashboard hiccup must not kill training."""
        from .harbor_eval import eval_predictions, upload_eval_rollouts

        eval_id: str | None = None
        create_eval = getattr(self.store, "create_eval", None)
        if callable(create_eval):
            try:
                rec = create_eval(
                    run_id=self.run_id,
                    benchmark_id=self.benchmark_id,
                    step=step,
                    metrics=metrics,
                )
                if isinstance(rec, dict):
                    eval_id = rec.get("id") or rec.get("eval_id")
                else:
                    eval_id = getattr(rec, "id", None)
            except Exception:  # pragma: no cover — defensive
                logger.exception("create_eval failed for run %s", self.run_id)
        # Each validation mints its own eval (tagged with `step`); the per-task
        # predictions hang off that eval_id so step-5 / step-10 / final evals
        # stay distinguishable. No eval_id → don't upload orphan predictions.
        if eval_id is None:
            logger.warning(
                "skipping val rollout upload for run %s step %s: no eval_id",
                self.run_id, step,
            )
            return
        try:
            preds = eval_predictions(tasks, groups, eval_id=eval_id, step=step)
            upload_eval_rollouts(self.store, self.run_id, preds)
        except Exception:  # pragma: no cover — defensive
            logger.exception("eval rollout upload failed for run %s", self.run_id)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;tasks&#x22;" type="&#x22;list[Any]&#x22;" value="null" />

    <PyParameter name="&#x22;groups&#x22;" type="&#x22;list[Any]&#x22;" value="null" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="null" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int | None&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, name, benchmark, tokenizer, run_every=0, max_tokens=256, temperature=0.0, breakdown_keys=list(), metrics=list(), chat_template=dict(), limit=None, engine='', model_name=None, workspace_dir=None, num_samples=1, n_concurrent=8, store=None, run_id=None, benchmark_id=None) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;benchmark&#x22;" type="&#x22;Benchmark&#x22;" value="null" />

    <PyParameter name="&#x22;tokenizer&#x22;" type="&#x22;Any&#x22;" value="null" />

    <PyParameter name="&#x22;run_every&#x22;" type="&#x22;int&#x22;" value="&#x22;0&#x22;" />

    <PyParameter name="&#x22;max_tokens&#x22;" type="&#x22;int&#x22;" value="&#x22;256&#x22;" />

    <PyParameter name="&#x22;temperature&#x22;" type="&#x22;float&#x22;" value="&#x22;0.0&#x22;" />

    <PyParameter name="&#x22;breakdown_keys&#x22;" type="&#x22;list[str]&#x22;" value="&#x22;list()&#x22;" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;list[str]&#x22;" value="&#x22;list()&#x22;" />

    <PyParameter name="&#x22;chat_template&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;dict()&#x22;" />

    <PyParameter name="&#x22;limit&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;engine&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

    <PyParameter name="&#x22;model_name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;workspace_dir&#x22;" type="&#x22;Any&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;num_samples&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />

    <PyParameter name="&#x22;n_concurrent&#x22;" type="&#x22;int&#x22;" value="&#x22;8&#x22;" />

    <PyParameter name="&#x22;store&#x22;" type="&#x22;Any&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;benchmark_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
