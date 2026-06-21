# _MockResult (/docs/evsys_sdk/training/backend/_MockResult)



Duck-typed ForwardBackwardResult / OptimStepResult.

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, **fields) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, **fields: Any) -> None:
        for k, v in fields.items():
            setattr(self, k, v)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;fields&#x22;" type="&#x22;Any&#x22;" value="&#x22;{}&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
