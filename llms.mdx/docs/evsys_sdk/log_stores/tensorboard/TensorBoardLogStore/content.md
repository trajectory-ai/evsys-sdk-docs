# TensorBoardLogStore (/docs/evsys_sdk/log_stores/tensorboard/TensorBoardLogStore)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'tensorboard'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;TensorBoardLogStoreConfig&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, log_dir, flush_secs=30) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, *, log_dir: str, flush_secs: int = 30) -> None:
        self._writer = SummaryWriter(log_dir=log_dir, flush_secs=flush_secs)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;log_dir&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;flush_secs&#x22;" type="&#x22;int&#x22;" value="&#x22;30&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;log_scalar&#x22;" type="&#x22;(self, key, value, step) -> None&#x22;">
  <PySourceCode>
    ```python
    def log_scalar(self, key: str, value: float, step: int) -> None:
        self._writer.add_scalar(key, value, global_step=step)
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
        for k, v in metrics.items():
            self._writer.add_scalar(k, v, global_step=step)
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
        # add_hparams is finicky; persist as text instead
        self._writer.add_text("hparams", str(params))
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
        self._writer.add_text(f"artifact/{name}", f"[{kind}] {path}")
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
        self._writer.close()
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
