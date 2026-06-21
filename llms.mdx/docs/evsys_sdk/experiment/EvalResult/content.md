# EvalResult (/docs/evsys_sdk/experiment/EvalResult)



One benchmark scored against one arm at one moment.

Multiple `EvalResult` entries can attach to an `ArmResult` when an
experiment carries several benchmarks under `metadata.benchmark`
(e.g. a `val` set + a `test` set). The `step` field disambiguates
in-loop validation rows (with their training-step value) from a
single post-training row (`step is None`).

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

<PyAttribute name="&#x22;benchmark_id&#x22;" type="&#x22;str | None&#x22;" value="null" />

<PyAttribute name="&#x22;metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="null" />

<PyAttribute name="&#x22;breakdowns&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />

<PyAttribute name="&#x22;eval_seconds&#x22;" type="&#x22;float&#x22;" value="null" />

<PyAttribute name="&#x22;step&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;">
  `None` → scored once post-training; int → in-loop at that step.
</PyAttribute>

<PyAttribute name="&#x22;tags&#x22;" type="&#x22;list[str]&#x22;" value="&#x22;field(default_factory=list)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, name, benchmark_id, metrics, breakdowns, eval_seconds, step=None, tags=list()) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;benchmark_id&#x22;" type="&#x22;str | None&#x22;" value="null" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="null" />

    <PyParameter name="&#x22;breakdowns&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />

    <PyParameter name="&#x22;eval_seconds&#x22;" type="&#x22;float&#x22;" value="null" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;tags&#x22;" type="&#x22;list[str]&#x22;" value="&#x22;list()&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
