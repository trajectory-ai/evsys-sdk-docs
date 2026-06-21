# LogStore (/docs/evsys_sdk/protocols/LogStore)



Where metrics, hyperparams, and artifact pointers go.

Implementations:

* `JSONLLogStore`      — append-only metrics.jsonl.
* `TensorBoardLogStore`— wraps SummaryWriter (optional dep).
* `SupabaseLogStore`   — persists rows to training\_metrics table.
* `MultiplexLogStore`  — fan out to several stores at once.

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

## Functions [#functions]

<PyFunction name="&#x22;log_scalar&#x22;" type="&#x22;(self, key, value, step) -> None&#x22;">
  <PySourceCode>
    ```python
    def log_scalar(self, key: str, value: float, step: int) -> None: ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;key&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;value&#x22;" type="&#x22;float&#x22;" value="null" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;log_metrics&#x22;" type="&#x22;(self, metrics, step) -> None&#x22;">
  <PySourceCode>
    ```python
    def log_metrics(self, metrics: dict[str, float], step: int) -> None: ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="null" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;log_hyperparams&#x22;" type="&#x22;(self, params) -> None&#x22;">
  <PySourceCode>
    ```python
    def log_hyperparams(self, params: dict[str, Any]) -> None: ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;params&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;log_artifact&#x22;" type="&#x22;(self, name, path, *, kind='file') -> None&#x22;">
  <PySourceCode>
    ```python
    def log_artifact(self, name: str, path: str, *, kind: str = "file") -> None: ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;path&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;kind&#x22;" type="&#x22;str&#x22;" value="&#x22;'file'&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;close&#x22;" type="&#x22;(self) -> None&#x22;">
  <PySourceCode>
    ```python
    def close(self) -> None: ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
