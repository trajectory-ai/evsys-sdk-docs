# EvsysStoreError (/docs/evsys_sdk/store/EvsysStoreError)



## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, status, body) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, status: int, body: str) -> None:
        super().__init__(f"sdk gateway → HTTP {status}: {body[:300]}")
        self.status, self.body = status, body
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;status&#x22;" type="&#x22;int&#x22;" value="null" />

    <PyParameter name="&#x22;body&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
