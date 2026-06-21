# Turn (/docs/evsys_sdk/training/trajectory/Turn)



One assistant turn of a rollout.

`prompt_tokens` is the full rendered context the policy saw for this turn
(system + prior turns + the latest observation); `completion_tokens` /
`logprobs` are the sampled response. A single-turn rollout has exactly one.

`logprobs` is optional — on-policy tinker rollouts populate it (training
needs it), but eval / closed-API rollouts often have none. Defaults to empty.

## Attributes [#attributes]

<PyAttribute name="&#x22;prompt_tokens&#x22;" type="&#x22;list[int]&#x22;" value="null" />

<PyAttribute name="&#x22;completion_tokens&#x22;" type="&#x22;list[int]&#x22;" value="null" />

<PyAttribute name="&#x22;logprobs&#x22;" type="&#x22;list[float]&#x22;" value="&#x22;field(default_factory=list)&#x22;" />

<PyAttribute name="&#x22;text&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, prompt_tokens, completion_tokens, logprobs=list(), text='') -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;prompt_tokens&#x22;" type="&#x22;list[int]&#x22;" value="null" />

    <PyParameter name="&#x22;completion_tokens&#x22;" type="&#x22;list[int]&#x22;" value="null" />

    <PyParameter name="&#x22;logprobs&#x22;" type="&#x22;list[float]&#x22;" value="&#x22;list()&#x22;" />

    <PyParameter name="&#x22;text&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
