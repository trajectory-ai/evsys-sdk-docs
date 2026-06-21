# JSONLLogStore (/docs/evsys_sdk/log_stores/jsonl/JSONLLogStore)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'jsonl'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;JSONLLogStoreConfig&#x22;" />

<PyAttribute name="&#x22;log_dir&#x22;" type="null" value="&#x22;Path(log_dir).expanduser()&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, log_dir, metrics_file='metrics.jsonl', hyperparams_file='hyperparams.json', artifacts_file='artifacts.json') -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(
        self,
        *,
        log_dir: str,
        metrics_file: str = "metrics.jsonl",
        hyperparams_file: str = "hyperparams.json",
        artifacts_file: str = "artifacts.json",
    ) -> None:
        self.log_dir = Path(log_dir).expanduser()
        self.log_dir.mkdir(parents=True, exist_ok=True)
        self._metrics_path = self.log_dir / metrics_file
        self._hyperparams_path = self.log_dir / hyperparams_file
        self._artifacts_path = self.log_dir / artifacts_file
        self._closed = False
        self._artifacts: list[dict[str, Any]] = []
        if self._artifacts_path.exists():
            try:
                self._artifacts = json.loads(self._artifacts_path.read_text())
            except json.JSONDecodeError:
                self._artifacts = []
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;log_dir&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;metrics_file&#x22;" type="&#x22;str&#x22;" value="&#x22;'metrics.jsonl'&#x22;" />

    <PyParameter name="&#x22;hyperparams_file&#x22;" type="&#x22;str&#x22;" value="&#x22;'hyperparams.json'&#x22;" />

    <PyParameter name="&#x22;artifacts_file&#x22;" type="&#x22;str&#x22;" value="&#x22;'artifacts.json'&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_append&#x22;" type="&#x22;(self, payload) -> None&#x22;">
  <PySourceCode>
    ```python
    def _append(self, payload: dict[str, Any]) -> None:
        if self._closed:
            raise RuntimeError("JSONLLogStore is closed")
        with self._metrics_path.open("a") as f:
            f.write(json.dumps(payload) + "\n")
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;payload&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;log_scalar&#x22;" type="&#x22;(self, key, value, step) -> None&#x22;">
  <PySourceCode>
    ```python
    def log_scalar(self, key: str, value: float, step: int) -> None:
        self._append({"ts": time.time(), "step": step, "metrics": {key: value}})
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
        self._append({"ts": time.time(), "step": step, "metrics": dict(metrics)})
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
        existing: dict[str, Any] = {}
        if self._hyperparams_path.exists():
            try:
                existing = json.loads(self._hyperparams_path.read_text())
            except json.JSONDecodeError:
                existing = {}
        existing.update(params)
        self._hyperparams_path.write_text(json.dumps(existing, indent=2, default=str))
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
        self._artifacts.append({"name": name, "path": path, "kind": kind, "ts": time.time()})
        self._artifacts_path.write_text(json.dumps(self._artifacts, indent=2))
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
        self._closed = True
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
