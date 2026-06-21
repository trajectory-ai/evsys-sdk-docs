# InMemoryDataStore (/docs/evsys_sdk/data_stores/in_memory/InMemoryDataStore)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'in_memory'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;InMemoryDataStoreConfig&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self) -> None:
        self._jsonl: dict[str, list[dict[str, Any]]] = {}
        self._json: dict[str, Any] = {}
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;read_jsonl&#x22;" type="&#x22;(self, path) -> list[dict[str, Any]]&#x22;">
  <PySourceCode>
    ```python
    def read_jsonl(self, path: str) -> list[dict[str, Any]]:
        if path not in self._jsonl:
            raise FileNotFoundError(path)
        return list(self._jsonl[path])
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;path&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.data_stores.in_memory.InMemoryDataStore.list[dict[str, typing.Any]]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;write_jsonl&#x22;" type="&#x22;(self, path, rows) -> None&#x22;">
  <PySourceCode>
    ```python
    def write_jsonl(self, path: str, rows: Iterable[dict[str, Any]]) -> None:
        self._jsonl[path] = list(rows)
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
        if path not in self._json:
            raise FileNotFoundError(path)
        return self._json[path]
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
        self._json[path] = value
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
        return path in self._jsonl or path in self._json
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
        return sorted(p for p in (*self._jsonl, *self._json) if p.startswith(prefix))
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;prefix&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.data_stores.in_memory.InMemoryDataStore.list[str]&#x22;" />
</PyFunction>
