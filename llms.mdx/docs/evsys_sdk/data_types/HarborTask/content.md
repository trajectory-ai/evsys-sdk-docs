# HarborTask (/docs/evsys_sdk/data_types/HarborTask)



One RL task — instruction + verifier spec.

Same shape whether the task came from a hand-written eval set, an HF
dataset, or a generated rollout corpus. Runners materialize the prompt by
feeding `instruction` to the policy, generate a rollout, and score it
using `verifier` (whichever variant — see `VerifierPayload`).

## Attributes [#attributes]

<PyAttribute name="&#x22;task_id&#x22;" type="&#x22;str&#x22;" value="null" />

<PyAttribute name="&#x22;instruction&#x22;" type="&#x22;str&#x22;" value="null" />

<PyAttribute name="&#x22;verifier&#x22;" type="&#x22;VerifierPayload&#x22;" value="null" />

<PyAttribute name="&#x22;metadata&#x22;" type="&#x22;dict&#x22;" value="&#x22;field(default_factory=dict)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, task_id, instruction, verifier, metadata=dict()) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;task_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;instruction&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;verifier&#x22;" type="&#x22;VerifierPayload&#x22;" value="null" />

    <PyParameter name="&#x22;metadata&#x22;" type="&#x22;dict&#x22;" value="&#x22;dict()&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
