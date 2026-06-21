# LocalExperimentStore (/docs/evsys_sdk/local_store/LocalExperimentStore)



Thread-safe filesystem mirror for experiments and generations.

## Attributes [#attributes]

<PyAttribute name="&#x22;root&#x22;" type="null" value="&#x22;resolve_log_dir(log_dir)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, log_dir=None) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, log_dir: str | None = None) -> None:
        self.root = resolve_log_dir(log_dir)
        self._lock = threading.Lock()
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;log_dir&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_exp_dir&#x22;" type="&#x22;(self, experiment_id) -> Path&#x22;">
  <PySourceCode>
    ```python
    def _exp_dir(self, experiment_id: str) -> Path:
        return self.root / "experiments" / str(experiment_id)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;experiment_id&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;pathlib.Path&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_gen_dir&#x22;" type="&#x22;(self, generation_id) -> Path&#x22;">
  <PySourceCode>
    ```python
    def _gen_dir(self, generation_id: str) -> Path:
        return self.root / "generations" / str(generation_id)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;generation_id&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;pathlib.Path&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_write_json&#x22;" type="&#x22;(path, payload) -> None&#x22;">
  <PySourceCode>
    ```python
    @staticmethod
    def _write_json(path: Path, payload: dict[str, Any]) -> None:
        path.parent.mkdir(parents=True, exist_ok=True)
        tmp = path.with_suffix(path.suffix + ".tmp")
        tmp.write_text(json.dumps(payload, indent=2, default=str))
        tmp.replace(path)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;path&#x22;" type="&#x22;Path&#x22;" value="null" />

    <PyParameter name="&#x22;payload&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_merge_json&#x22;" type="&#x22;(path, patch) -> None&#x22;">
  <PySourceCode>
    ```python
    @staticmethod
    def _merge_json(path: Path, patch: dict[str, Any]) -> None:
        existing: dict[str, Any] = {}
        if path.exists():
            try:
                existing = json.loads(path.read_text())
            except Exception:
                existing = {}
        existing.update(patch)
        existing["_updated_at"] = time.time()
        LocalExperimentStore._write_json(path, existing)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;path&#x22;" type="&#x22;Path&#x22;" value="null" />

    <PyParameter name="&#x22;patch&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_append_jsonl&#x22;" type="&#x22;(path, row) -> None&#x22;">
  <PySourceCode>
    ```python
    @staticmethod
    def _append_jsonl(path: Path, row: dict[str, Any]) -> None:
        path.parent.mkdir(parents=True, exist_ok=True)
        with path.open("a") as f:
            f.write(json.dumps(row, default=str) + "\n")
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;path&#x22;" type="&#x22;Path&#x22;" value="null" />

    <PyParameter name="&#x22;row&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;create_experiment&#x22;" type="&#x22;(self, experiment_id, payload) -> None&#x22;">
  <PySourceCode>
    ```python
    def create_experiment(self, experiment_id: str, payload: dict[str, Any]) -> None:
        with self._lock:
            row = {"id": experiment_id, "_created_at": time.time(), **payload}
            self._write_json(self._exp_dir(experiment_id) / LOCAL_EXPERIMENT_FILE, row)
        log.debug("local: wrote experiment %s", experiment_id)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;experiment_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;payload&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;update_experiment&#x22;" type="&#x22;(self, experiment_id, patch) -> None&#x22;">
  <PySourceCode>
    ```python
    def update_experiment(self, experiment_id: str, patch: dict[str, Any]) -> None:
        with self._lock:
            self._merge_json(self._exp_dir(experiment_id) / LOCAL_EXPERIMENT_FILE, patch)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;experiment_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;patch&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;create_run&#x22;" type="&#x22;(self, run_id, payload) -> None&#x22;">
  <PySourceCode>
    ```python
    def create_run(self, run_id: str, payload: dict[str, Any]) -> None:
        with self._lock:
            row = {"id": run_id, "_created_at": time.time(), **payload}
            self._write_json(self._gen_dir(run_id) / LOCAL_GENERATION_FILE, row)
        log.debug("local: wrote run %s", run_id)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;payload&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;update_run&#x22;" type="&#x22;(self, run_id, patch) -> None&#x22;">
  <PySourceCode>
    ```python
    def update_run(self, run_id: str, patch: dict[str, Any]) -> None:
        with self._lock:
            self._merge_json(self._gen_dir(run_id) / LOCAL_GENERATION_FILE, patch)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;patch&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;log_step&#x22;" type="&#x22;(self, generation_id, body) -> None&#x22;">
  <PySourceCode>
    ```python
    def log_step(self, generation_id: str, body: dict[str, Any]) -> None:
        with self._lock:
            self._append_jsonl(self._gen_dir(generation_id) / LOCAL_METRICS_FILE, body)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;generation_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;body&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;log_eval&#x22;" type="&#x22;(self, generation_id, body) -> None&#x22;">
  <PySourceCode>
    ```python
    def log_eval(self, generation_id: str, body: dict[str, Any]) -> None:
        with self._lock:
            self._append_jsonl(self._gen_dir(generation_id) / LOCAL_EVALS_FILE, body)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;generation_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;body&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;log_predictions&#x22;" type="&#x22;(self, generation_id, predictions) -> None&#x22;">
  <PySourceCode>
    ```python
    def log_predictions(self, generation_id: str, predictions: list[dict[str, Any]]) -> None:
        with self._lock:
            path = self._gen_dir(generation_id) / LOCAL_PREDICTIONS_FILE
            for p in predictions:
                self._append_jsonl(path, p)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;generation_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;predictions&#x22;" type="&#x22;list[dict[str, Any]]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
