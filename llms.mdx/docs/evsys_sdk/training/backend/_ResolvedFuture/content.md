# _ResolvedFuture (/docs/evsys_sdk/training/backend/_ResolvedFuture)



Tinker-style future with `.result_async()` resolving to a value.

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, value) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, value: Any) -> None:
        self._value = value
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;value&#x22;" type="&#x22;Any&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;result_async&#x22;" type="&#x22;(self) -> Any&#x22;">
  <PySourceCode>
    ```python
    async def result_async(self) -> Any:
        return self._value
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
</PyFunction>

<PyFunction name="&#x22;result&#x22;" type="&#x22;(self) -> Any&#x22;">
  <PySourceCode>
    ```python
    def result(self) -> Any:
        return self._value
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
</PyFunction>
