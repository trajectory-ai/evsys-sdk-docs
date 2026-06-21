# DataStore (/docs/evsys_sdk/protocols/DataStore)



Abstract data sink+source.

Implementations:

* `LocalDataStore` — filesystem JSONL/parquet/json, no network.
* `SupabaseDataStore` — REST against PostgREST tables.
* `InMemoryDataStore` — for tests.

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

## Functions [#functions]

<PyFunction name="&#x22;read_jsonl&#x22;" type="&#x22;(self, path) -> list[dict[str, Any]]&#x22;">
  <PySourceCode>
    ```python
    def read_jsonl(self, path: str) -> list[dict[str, Any]]: ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;path&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.protocols.DataStore.list[dict[str, typing.Any]]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;write_jsonl&#x22;" type="&#x22;(self, path, rows) -> None&#x22;">
  <PySourceCode>
    ```python
    def write_jsonl(self, path: str, rows: Iterable[dict[str, Any]]) -> None: ...
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
    def read_json(self, path: str) -> Any: ...
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
    def write_json(self, path: str, value: Any) -> None: ...
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
    def exists(self, path: str) -> bool: ...
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
    def list(self, prefix: str) -> list[str]: ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;prefix&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.protocols.DataStore.list[str]&#x22;" />
</PyFunction>
