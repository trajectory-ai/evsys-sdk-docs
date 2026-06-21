# FormatOnlyVerifier (/docs/evsys_sdk/verifiers/format_only/FormatOnlyVerifier)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'format_only'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;FormatOnlyConfig&#x22;" />

<PyAttribute name="&#x22;has_think_reward&#x22;" type="null" value="&#x22;has_think_reward&#x22;" />

<PyAttribute name="&#x22;has_answer_reward&#x22;" type="null" value="&#x22;has_answer_reward&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, has_think_reward=0.5, has_answer_reward=0.5) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, *, has_think_reward: float = 0.5, has_answer_reward: float = 0.5) -> None:
        self.has_think_reward = has_think_reward
        self.has_answer_reward = has_answer_reward
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;has_think_reward&#x22;" type="&#x22;float&#x22;" value="&#x22;0.5&#x22;" />

    <PyParameter name="&#x22;has_answer_reward&#x22;" type="&#x22;float&#x22;" value="&#x22;0.5&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;verify&#x22;" type="&#x22;(self, *, prompt, completion, target) -> VerificationResult&#x22;">
  <PySourceCode>
    ```python
    def verify(self, *, prompt: str, completion: str, target: dict[str, Any]) -> VerificationResult:
        has_think = "<think>" in completion and "</think>" in completion
        has_answer = "<answer>" in completion and "</answer>" in completion
        r = 0.0
        if has_think:
            r += self.has_think_reward
        if has_answer:
            r += self.has_answer_reward
        return VerificationResult(reward=r, info={"has_think": has_think, "has_answer": has_answer})
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;prompt&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;completion&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;target&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.protocols.VerificationResult&#x22;" />
</PyFunction>
