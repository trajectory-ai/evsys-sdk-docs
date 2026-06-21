# experiment (/docs/evsys_sdk/experiment)



Experiment — the top-level OOP orchestrator.

What it replaces in researcher scripts: the manual
`create_experiment` → `create_group` → per-arm `create_run` →
`run_experiment(cfg)` → `create_eval` → `set_conclusion` choreography.
Today every sweep script hand-rolls that loop. An `Experiment` collapses it
to one declarative `.run()` call.

Usage:

# experiments/\<date>\_\<slug>/run.py [#experimentsdate_slugrunpy]

from evsys\_sdk import Experiment
import src   # registers custom verifiers/transforms

Experiment.from\_yaml("config.yaml").run()

Per-arm failure isolation: if one sweep arm raises during training, it gets
marked `status=failed` on the dashboard and the remaining arms continue.
The experiment finishes `completed` if any arm succeeded.

Config carries the project-shaped fields under `metadata`:

metadata:
hypothesis: "..."
tags: \["sft", "qwen3\_4b"]
project\_goal\_id: "..."
success\_metric: "pass\_rate"   # which metric ranks arms for best\_score
benchmark:                    # post-training eval (optional)
path: "data/benchmark/\<name>"
id: "\<dashboard benchmark id>"
breakdown\_keys: \["toolkit"]

Dependencies are injectable for testing:

* `store`: EvsysStore (None → skip dashboard records, run locally)
* `train_fn`: `(cfg) -> list[RunResult]` (default: `runner.run_experiment`)
* `benchmark`: `Benchmark` (overrides metadata.benchmark.path)
* `inference_factory`: `(RunResult, RunConfig) -> InferenceClient`
  (called once per completed arm to build the eval client)

<PyAttribute name="&#x22;logger&#x22;" type="null" value="&#x22;logging.getLogger(__name__)&#x22;" />

<PyAttribute name="&#x22;TrainFn&#x22;" type="null" value="&#x22;Callable[[ExperimentConfig], list[RunResult]]&#x22;" />

<PyAttribute name="&#x22;InferenceFactory&#x22;" type="null" value="&#x22;Callable[[RunResult, RunConfig], InferenceClient]&#x22;" />

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['ArmResult', 'EvalResult', 'Experiment', 'ExperimentResult', 'TrainFn', 'InferenceFactory']&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;EvalResult&#x22;" href="&#x22;/docs/evsys_sdk/experiment/EvalResult&#x22;" />

      <Card title="&#x22;ArmResult&#x22;" href="&#x22;/docs/evsys_sdk/experiment/ArmResult&#x22;" />

      <Card title="&#x22;ExperimentResult&#x22;" href="&#x22;/docs/evsys_sdk/experiment/ExperimentResult&#x22;" />

      <Card title="&#x22;Experiment&#x22;" href="&#x22;/docs/evsys_sdk/experiment/Experiment&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;_benchmark_models&#x22;" type="&#x22;(bench_meta) -> list[str]&#x22;">
      API models a benchmark should also be scored on, from `models` (list)
      or `model` (single string) on the benchmark spec. Empty when neither —
      i.e. checkpoint-only, the existing behavior.

      <PySourceCode>
        ```python
        def _benchmark_models(bench_meta: dict) -> list[str]:
            """API models a benchmark should also be scored on, from ``models`` (list)
            or ``model`` (single string) on the benchmark spec. Empty when neither —
            i.e. checkpoint-only, the existing behavior."""
            raw = bench_meta.get("models")
            if raw is None:
                raw = bench_meta.get("model")
            if not raw:
                return []
            return [str(raw)] if isinstance(raw, str) else [str(m) for m in raw]
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;bench_meta&#x22;" type="&#x22;dict&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;list[str]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_predictions_from_score&#x22;" type="&#x22;(score) -> list[dict]&#x22;">
      Prediction rows for the in-process (non-harbor) eval path, one per task,
      in the same shape `harbor_eval.eval_predictions` produces — so logger
      callbacks get the model output + reward regardless of engine.

      <PySourceCode>
        ```python
        def _predictions_from_score(score: BenchmarkScore) -> list[dict]:
            """Prediction rows for the in-process (non-harbor) eval path, one per task,
            in the same shape ``harbor_eval.eval_predictions`` produces — so logger
            callbacks get the model output + reward regardless of engine."""
            return [
                {
                    "kind": "eval",
                    "task_id": r.task_id,
                    "instruction": r.instruction,
                    "expected": r.expected,
                    "reward": r.reward,
                    "output": r.model_output,
                    "metadata": dict(r.metadata or {}),
                }
                for r in (score.per_task or [])
            ]
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;score&#x22;" type="&#x22;BenchmarkScore&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;list[dict]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_default_train_fn&#x22;" type="&#x22;(cfg) -> list[RunResult]&#x22;">
      <PySourceCode>
        ```python
        def _default_train_fn(cfg: ExperimentConfig) -> list[RunResult]:
            from .runner import run_experiment

            return run_experiment(cfg)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;cfg&#x22;" type="&#x22;ExperimentConfig&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;list[evsys_sdk.protocols.RunResult]&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
