# LocalDataStore (/docs/evsys_sdk/data_stores/local/LocalDataStore)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'local'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;LocalDataStoreConfig&#x22;" />

<PyAttribute name="&#x22;root&#x22;" type="null" value="&#x22;Path(root).expanduser().resolve()&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, root='.') -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, *, root: str | os.PathLike[str] = ".") -> None:
        self.root = Path(root).expanduser().resolve()
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;root&#x22;" type="&#x22;str | os.PathLike[str]&#x22;" value="&#x22;'.'&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_resolve&#x22;" type="&#x22;(self, path) -> Path&#x22;">
  <PySourceCode>
    ```python
    def _resolve(self, path: str) -> Path:
        p = Path(path).expanduser()
        if p.is_absolute():
            return p
        return self.root / p
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;path&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;pathlib.Path&#x22;" />
</PyFunction>

<PyFunction name="&#x22;read_jsonl&#x22;" type="&#x22;(self, path) -> list[dict[str, Any]]&#x22;">
  <PySourceCode>
    ```python
    def read_jsonl(self, path: str) -> list[dict[str, Any]]:
        with self._resolve(path).open() as f:
            return [json.loads(line) for line in f if line.strip()]
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;path&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.data_stores.local.LocalDataStore.list[dict[str, typing.Any]]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;write_jsonl&#x22;" type="&#x22;(self, path, rows) -> None&#x22;">
  <PySourceCode>
    ```python
    def write_jsonl(self, path: str, rows: Iterable[dict[str, Any]]) -> None:
        target = self._resolve(path)
        target.parent.mkdir(parents=True, exist_ok=True)
        with target.open("w") as f:
            for r in rows:
                f.write(json.dumps(r) + "\n")
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;path&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;rows&#x22;" type="&#x22;Iterable[dict[str, Any]]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;read_json&#x22;" type="&#x22;(self, path) -> Any&#x22;">
  <PySourceCode>
    ```python
    def read_json(self, path: str) -> Any:
        with self._resolve(path).open() as f:
            return json.load(f)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;path&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
</PyFunction>

<PyFunction name="&#x22;write_json&#x22;" type="&#x22;(self, path, value) -> None&#x22;">
  <PySourceCode>
    ```python
    def write_json(self, path: str, value: Any) -> None:
        target = self._resolve(path)
        target.parent.mkdir(parents=True, exist_ok=True)
        with target.open("w") as f:
            json.dump(value, f, indent=2, default=str)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;path&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;value&#x22;" type="&#x22;Any&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;exists&#x22;" type="&#x22;(self, path) -> bool&#x22;">
  <PySourceCode>
    ```python
    def exists(self, path: str) -> bool:
        return self._resolve(path).exists()
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;path&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;bool&#x22;" />
</PyFunction>

<PyFunction name="&#x22;list&#x22;" type="&#x22;(self, prefix) -> list[str]&#x22;">
  <PySourceCode>
    ```python
    def list(self, prefix: str) -> list[str]:
        base = self._resolve(prefix)
        if base.is_dir():
            return sorted(str(p.relative_to(self.root)) for p in base.rglob("*") if p.is_file())
        return [str(p.relative_to(self.root)) for p in self.root.glob(prefix) if p.is_file()]
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;prefix&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.data_stores.local.LocalDataStore.list[str]&#x22;" />
</PyFunction>
