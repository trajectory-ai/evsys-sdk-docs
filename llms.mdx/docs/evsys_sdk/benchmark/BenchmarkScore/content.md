# BenchmarkScore (/docs/evsys_sdk/benchmark/BenchmarkScore)



Aggregate output of `Benchmark.score`.

## Attributes [#attributes]

<PyAttribute name="&#x22;metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="null">
  Top-level aggregates: `mean_reward`, `pass_rate`, `n_tasks`.
</PyAttribute>

<PyAttribute name="&#x22;per_task&#x22;" type="&#x22;list[BenchmarkTaskResult]&#x22;" value="null">
  One entry per task in the same order as `Benchmark.tasks`.
</PyAttribute>

<PyAttribute name="&#x22;breakdowns&#x22;" type="&#x22;dict[str, dict[str, dict[str, float]]]&#x22;" value="&#x22;field(default_factory=dict)&#x22;">
  `\{bucket_field: \{bucket_value: \{n, mean_reward, pass_rate\}\}\}`.

  Populated when `score(..., breakdown_keys=[...])` is passed. Each bucket
  field is an attribute path into a task's `metadata` (e.g. `"toolkit"`).
</PyAttribute>

<PyAttribute name="&#x22;rollouts&#x22;" type="&#x22;list['TrajectoryGroup']&#x22;" value="&#x22;field(default_factory=list)&#x22;">
  Raw per-(task, sample) harbor rollouts (token ids + reward + usage), in
  task order; populated by `score_via_harbor`, empty for in-process `score()`.
  Lets callers upload per-sample eval predictions without re-running.
</PyAttribute>

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, metrics, per_task, breakdowns=dict(), rollouts=list()) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="null" />

    <PyParameter name="&#x22;per_task&#x22;" type="&#x22;list[BenchmarkTaskResult]&#x22;" value="null" />

    <PyParameter name="&#x22;breakdowns&#x22;" type="&#x22;dict[str, dict[str, dict[str, float]]]&#x22;" value="&#x22;dict()&#x22;" />

    <PyParameter name="&#x22;rollouts&#x22;" type="&#x22;list['TrajectoryGroup']&#x22;" value="&#x22;list()&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
