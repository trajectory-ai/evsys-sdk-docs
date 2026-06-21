# evaluators (/docs/evsys_sdk/training/evaluators)



In-loop evaluator adapters — wrap an :class:`evsys_sdk.Benchmark`
so the training loop can score it every N steps against the live
sampler.

The post-training scoring path (`Experiment._eval_arm` →
`Benchmark.score(client)`) takes a *synchronous* `InferenceClient`.
The training-loop eval slot hands evaluators a live *async*
`SamplingClient`. :class:`_AsyncToSyncSampler` bridges the two with
`asyncio.run_coroutine_threadsafe` + `asyncio.to_thread` so the
Benchmark iteration doesn't block the event loop and the existing
`ChatTemplatedInference` wrapper (which inspects `_tokenizer`) keeps
working unchanged.

The other half of the wiring is :func:`build_in_loop_evaluators` — a
metadata-aware factory that the three native algorithm composers call to
turn `metadata.benchmark` list entries with a `run_every` field into
:class:`BenchmarkEvaluator` instances ready for the
:class:`~evsys_sdk.training.loop.TrainingLoop` evaluators list.

<PyAttribute name="&#x22;logger&#x22;" type="null" value="&#x22;logging.getLogger(__name__)&#x22;" />

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['BenchmarkEvaluator', 'build_in_loop_evaluators']&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;_AsyncToSyncSampler&#x22;" href="&#x22;/docs/evsys_sdk/training/evaluators/_AsyncToSyncSampler&#x22;" />

      <Card title="&#x22;BenchmarkEvaluator&#x22;" href="&#x22;/docs/evsys_sdk/training/evaluators/BenchmarkEvaluator&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;build_in_loop_evaluators&#x22;" type="&#x22;(metadata, *, tokenizer, store=None, model_name=None, workspace_dir=None, run_id=None) -> list[BenchmarkEvaluator]&#x22;">
      Read `metadata.benchmark` and return one
      :class:`BenchmarkEvaluator` per entry whose `run_every` > 0.

      Single-dict `benchmark` and list form are both accepted (matches the
      parser in :meth:`evsys_sdk.experiment.Experiment._resolve_benchmarks`).
      Entries without `run_every` are post-training-only and silently
      skipped here — they're handled by `Experiment._eval_arm`.

      `tokenizer` is required (used by the async→sync sampler bridge);
      `store` is required when an entry resolves a benchmark by `id` or
      `name` rather than `path`.

      <PySourceCode>
        ```python
        def build_in_loop_evaluators(
            metadata: dict[str, Any] | None,
            *,
            tokenizer: Any,
            store: Any = None,
            model_name: str | None = None,
            workspace_dir: Any = None,
            run_id: str | None = None,
        ) -> list[BenchmarkEvaluator]:
            """Read ``metadata.benchmark`` and return one
            :class:`BenchmarkEvaluator` per entry whose ``run_every`` > 0.

            Single-dict ``benchmark`` and list form are both accepted (matches the
            parser in :meth:`evsys_sdk.experiment.Experiment._resolve_benchmarks`).
            Entries without ``run_every`` are post-training-only and silently
            skipped here — they're handled by ``Experiment._eval_arm``.

            ``tokenizer`` is required (used by the async→sync sampler bridge);
            ``store`` is required when an entry resolves a benchmark by ``id`` or
            ``name`` rather than ``path``.
            """
            if not metadata:
                return []
            raw = metadata.get("benchmark")
            if not raw:
                return []
            specs: list[dict[str, Any]] = (
                [raw] if isinstance(raw, dict) else list(raw)
            )
            out: list[BenchmarkEvaluator] = []
            for i, spec in enumerate(specs):
                if not isinstance(spec, dict):
                    logger.warning(
                        "build_in_loop_evaluators: benchmark[%d] not a dict — skipping",
                        i,
                    )
                    continue
                run_every = int(spec.get("run_every") or 0)
                if run_every <= 0:
                    continue
                bench = Benchmark.load(spec, store=store)
                if bench is None:
                    logger.warning(
                        "build_in_loop_evaluators: benchmark[%d] (%r) didn't resolve — skipping",
                        i, spec.get("name"),
                    )
                    continue
                out.append(BenchmarkEvaluator(
                    name=str(spec.get("name", f"benchmark_{i}")),
                    benchmark=bench,
                    tokenizer=tokenizer,
                    run_every=run_every,
                    max_tokens=int(spec.get("max_tokens", 256)),
                    temperature=float(spec.get("temperature", 0.0)),
                    breakdown_keys=list(spec.get("breakdown_keys") or []),
                    metrics=list(spec.get("metrics") or []),
                    chat_template=dict(spec.get("chat_template") or {}),
                    limit=int(spec["limit"]) if spec.get("limit") is not None else None,
                    engine=str(spec.get("engine", "")),
                    model_name=model_name,
                    workspace_dir=workspace_dir,
                    num_samples=int(spec.get("num_samples", 1)),
                    n_concurrent=int(spec.get("n_concurrent", 8)),
                    store=store,
                    run_id=run_id,
                    benchmark_id=(str(spec["id"]) if spec.get("id") is not None else None),
                ))
            return out
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;metadata&#x22;" type="&#x22;dict[str, Any] | None&#x22;" value="null" />

        <PyParameter name="&#x22;tokenizer&#x22;" type="&#x22;Any&#x22;" value="null" />

        <PyParameter name="&#x22;store&#x22;" type="&#x22;Any&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;model_name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;workspace_dir&#x22;" type="&#x22;Any&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="&#x22;list[evsys_sdk.training.evaluators.BenchmarkEvaluator]&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
