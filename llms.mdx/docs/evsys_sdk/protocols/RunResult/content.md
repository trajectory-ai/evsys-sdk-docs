# RunResult (/docs/evsys_sdk/protocols/RunResult)



What an algorithm returns from .train(ctx).

## Attributes [#attributes]

<PyAttribute name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />

<PyAttribute name="&#x22;status&#x22;" type="&#x22;str&#x22;" value="null">
  One of: 'completed', 'failed', 'cancelled'.
</PyAttribute>

<PyAttribute name="&#x22;metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="&#x22;field(default_factory=dict)&#x22;">
  Final aggregated metrics for the run.
</PyAttribute>

<PyAttribute name="&#x22;artifacts&#x22;" type="&#x22;dict[str, str]&#x22;" value="&#x22;field(default_factory=dict)&#x22;">
  Named artifact paths (e.g. \{'final\_checkpoint': 's3://...', ...}).
</PyAttribute>

<PyAttribute name="&#x22;error&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  If status != 'completed', a short human-readable reason.
</PyAttribute>

<PyAttribute name="&#x22;extras&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;field(default_factory=dict)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, run_id, status, metrics=dict(), artifacts=dict(), error=None, extras=dict()) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;status&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="&#x22;dict()&#x22;" />

    <PyParameter name="&#x22;artifacts&#x22;" type="&#x22;dict[str, str]&#x22;" value="&#x22;dict()&#x22;" />

    <PyParameter name="&#x22;error&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;extras&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;dict()&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
