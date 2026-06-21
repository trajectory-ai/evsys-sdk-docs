# MultiplexLogStore (/docs/evsys_sdk/log_stores/multiplex/MultiplexLogStore)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'multiplex'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;MultiplexLogStoreConfig&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, children=None) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, *, children: list[dict[str, Any]] | None = None) -> None:
        self._children = []
        for spec in children or []:
            cls = get_log_store(spec["kind"])
            self._children.append(cls(**spec.get("params", {})))
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;children&#x22;" type="&#x22;list[dict[str, Any]] | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;log_scalar&#x22;" type="&#x22;(self, key, value, step) -> None&#x22;">
  <PySourceCode>
    ```python
    def log_scalar(self, key: str, value: float, step: int) -> None:
        for c in self._children:
            c.log_scalar(key, value, step)
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
    def log_metrics(self, metrics: dict[str, float], step: int) -> None:
        for c in self._children:
            c.log_metrics(metrics, step)
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
    def log_hyperparams(self, params: dict[str, Any]) -> None:
        for c in self._children:
            c.log_hyperparams(params)
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
    def log_artifact(self, name: str, path: str, *, kind: str = "file") -> None:
        for c in self._children:
            c.log_artifact(name, path, kind=kind)
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
    def close(self) -> None:
        for c in self._children:
            try:
                c.close()
            except Exception:
                pass
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
