# benchmark_run (/docs/evsys_sdk/benchmark_run)



Standalone benchmark run — score a benchmark on a model, no training.

Reuses the *same* harbor rollout path as in-training eval
(:func:`evsys_sdk.training.harbor_eval.score_via_harbor`) plus the same metrics
and eval-rollout upload. The only differences from a post-training eval: there's
no checkpoint, and the rollout LLM is a closed / API model via litellm
(`model_client="litellm"`). Rollouts persist under the local `.evsys/`
workspace and, when a `store` + `run_id` are given, push to the dashboard.

from evsys\_sdk import run\_benchmark

# by local path, dashboard id, or name — same resolver as the config [#by-local-path-dashboard-id-or-name--same-resolver-as-the-config]

metrics = run\_benchmark(path="data/benchmark/tool-search",
model="anthropic/claude-opus-4-1")
metrics = run\_benchmark(id="bench\_abc123", model="openai/gpt-4o", store=store)

API keys come from the standard provider env vars (`ANTHROPIC_API_KEY`,
`OPENAI_API_KEY`, …).

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['run_benchmark']&#x22;" />

<Tabs items="[&#x22;Functions&#x22;]">
  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;run_benchmark&#x22;" type="&#x22;(benchmark=None, *, model, path=None, id=None, name=None, num_samples=1, max_tokens=512, temperature=0.0, system_prompt=None, limit=None, n_concurrent=8, workspace_dir=None, store=None, run_id=None, eval_id=None) -> dict[str, float]&#x22;">
      Score a benchmark on a closed / API `model` through harbor — no training.

      The benchmark is given directly (`benchmark=`) or resolved by
      `path` / `id` / `name` via the shared :meth:`Benchmark.load` resolver
      (same references the config accepts). `model` is a litellm string, e.g.
      `"anthropic/claude-opus-4-1"` / `"openai/gpt-4o"`; repeats use the
      per-task `num_samples` in one async harbor job. Returns the eval metric
      dict (mean\_reward, pass\_rate, n\_tasks, and time/tokens/cost per task), and
      uploads the per-task eval rollouts when both `store` and `run_id` are set.

      <PySourceCode>
        ```python
        def run_benchmark(
            benchmark: Benchmark | None = None,
            *,
            model: str,
            path: str | None = None,
            id: str | None = None,
            name: str | None = None,
            num_samples: int = 1,
            max_tokens: int = 512,
            temperature: float = 0.0,
            system_prompt: str | None = None,
            limit: int | None = None,
            n_concurrent: int = 8,
            workspace_dir: str | Path | None = None,
            store: Any = None,
            run_id: str | None = None,
            eval_id: str | None = None,
        ) -> dict[str, float]:
            """Score a benchmark on a closed / API ``model`` through harbor — no training.

            The benchmark is given directly (``benchmark=``) or resolved by
            ``path`` / ``id`` / ``name`` via the shared :meth:`Benchmark.load` resolver
            (same references the config accepts). ``model`` is a litellm string, e.g.
            ``"anthropic/claude-opus-4-1"`` / ``"openai/gpt-4o"``; repeats use the
            per-task ``num_samples`` in one async harbor job. Returns the eval metric
            dict (mean_reward, pass_rate, n_tasks, and time/tokens/cost per task), and
            uploads the per-task eval rollouts when both ``store`` and ``run_id`` are set.
            """
            import asyncio
            import tempfile

            from .training.harbor_eval import eval_predictions, upload_eval_rollouts

            if benchmark is None:
                benchmark = Benchmark.load({"path": path, "id": id, "name": name}, store=store)
                if benchmark is None:
                    raise ValueError("run_benchmark: pass a Benchmark or one of path / id / name")

            tasks = benchmark.tasks if limit is None else benchmark.tasks[: max(0, int(limit))]

            # Persist rollouts under the local .evsys/ workspace (not an ephemeral
            # tempdir) when we can; fall back to a tempdir if .evsys isn't writable.
            if workspace_dir is not None:
                ws = Path(workspace_dir)
            else:
                safe = f"{benchmark.name}_{model}".replace("/", "_").replace(" ", "_")
                ws = Path(_WORKSPACE_ROOT) / "outputs" / "benchmark_runs" / safe
            try:
                ws.mkdir(parents=True, exist_ok=True)
            except OSError:
                ws = Path(tempfile.mkdtemp(prefix="evsys_bench_"))

            score = asyncio.run(benchmark.score_via_harbor(
                model_name=model,
                model_path=None,            # no checkpoint — the API model *is* the policy
                model_client="litellm",
                workspace_dir=ws,
                num_samples=num_samples,
                max_tokens=max_tokens,
                temperature=temperature,
                system_prompt=system_prompt,
                limit=limit,
                n_concurrent=n_concurrent,
            ))

            if store is not None and run_id:
                preds = eval_predictions(tasks, score.rollouts, eval_id=eval_id, step=None)
                upload_eval_rollouts(store, run_id, preds)
            return score.metrics
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;benchmark&#x22;" type="&#x22;Benchmark | None&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;model&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;num_samples&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />

        <PyParameter name="&#x22;max_tokens&#x22;" type="&#x22;int&#x22;" value="&#x22;512&#x22;" />

        <PyParameter name="&#x22;temperature&#x22;" type="&#x22;float&#x22;" value="&#x22;0.0&#x22;" />

        <PyParameter name="&#x22;system_prompt&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;limit&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;n_concurrent&#x22;" type="&#x22;int&#x22;" value="&#x22;8&#x22;" />

        <PyParameter name="&#x22;workspace_dir&#x22;" type="&#x22;str | Path | None&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;store&#x22;" type="&#x22;Any&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;eval_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="&#x22;dict[str, float]&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
