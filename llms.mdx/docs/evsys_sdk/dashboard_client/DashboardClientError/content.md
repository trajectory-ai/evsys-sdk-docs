# DashboardClientError (/docs/evsys_sdk/dashboard_client/DashboardClientError)



Raised on a 4xx response (auth / membership / bad request).

## Attributes [#attributes]

<PyAttribute name="&#x22;status&#x22;" type="null" value="&#x22;status&#x22;" />

<PyAttribute name="&#x22;body&#x22;" type="null" value="&#x22;body&#x22;" />

<PyAttribute name="&#x22;path&#x22;" type="null" value="&#x22;path&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, status, body, path) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, status: int, body: str, path: str) -> None:
        super().__init__(f"{path} → HTTP {status}: {body[:200]}")
        self.status = status
        self.body = body
        self.path = path
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;status&#x22;" type="&#x22;int&#x22;" value="null" />

    <PyParameter name="&#x22;body&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;path&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
