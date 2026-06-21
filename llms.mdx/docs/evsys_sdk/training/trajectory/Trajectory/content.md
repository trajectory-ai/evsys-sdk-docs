# Trajectory (/docs/evsys_sdk/training/trajectory/Trajectory)



One rollout: an ordered list of :class:`Turn`\s + a scalar reward.

## Attributes [#attributes]

<PyAttribute name="&#x22;turns&#x22;" type="&#x22;list[Turn]&#x22;" value="null" />

<PyAttribute name="&#x22;reward&#x22;" type="&#x22;float&#x22;" value="&#x22;0.0&#x22;" />

<PyAttribute name="&#x22;metadata&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;field(default_factory=dict)&#x22;">
  Free-form per-rollout extras. The harbor harvest stamps a `"usage"`
  dict here — `\{cost_usd, prompt_tokens, completion_tokens, cached_tokens,
  latency_s\}` (any field harbor didn't report is `None`) — which the eval
  aggregator turns into the default `time_per_task` / `tokens_per_task` /
  `cost_per_task` metrics.
</PyAttribute>

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, turns, reward=0.0, metadata=dict()) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;turns&#x22;" type="&#x22;list[Turn]&#x22;" value="null" />

    <PyParameter name="&#x22;reward&#x22;" type="&#x22;float&#x22;" value="&#x22;0.0&#x22;" />

    <PyParameter name="&#x22;metadata&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;dict()&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
