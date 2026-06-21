# Verifier (/docs/evsys_sdk/protocols/Verifier)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="null" />

## Functions [#functions]

<PyFunction name="&#x22;verify&#x22;" type="&#x22;(self, *, prompt, completion, target) -> VerificationResult&#x22;">
  <PySourceCode>
    ```python
    def verify(
        self,
        *,
        prompt: str,
        completion: str,
        target: dict[str, Any],
    ) -> VerificationResult: ...
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
