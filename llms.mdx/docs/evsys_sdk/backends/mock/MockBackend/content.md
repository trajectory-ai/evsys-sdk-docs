# MockBackend (/docs/evsys_sdk/backends/mock/MockBackend)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'mock'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;MockBackendConfig&#x22;" />

<PyAttribute name="&#x22;fail_on_prepare&#x22;" type="null" value="&#x22;fail_on_prepare&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, fail_on_prepare=False) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, *, fail_on_prepare: bool = False) -> None:
        self.fail_on_prepare = fail_on_prepare
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;fail_on_prepare&#x22;" type="&#x22;bool&#x22;" value="&#x22;False&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;prepare&#x22;" type="&#x22;(self, *, model, run_dir) -> dict[str, Any]&#x22;">
  <PySourceCode>
    ```python
    def prepare(self, *, model: dict[str, Any], run_dir: str) -> dict[str, Any]:
        if self.fail_on_prepare:
            raise RuntimeError("MockBackend asked to fail")
        return {
            "backend": "mock",
            "model": dict(model),
            "run_dir": run_dir,
        }
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;model&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />

    <PyParameter name="&#x22;run_dir&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;dict[str, typing.Any]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;teardown&#x22;" type="&#x22;(self, handles) -> None&#x22;">
  <PySourceCode>
    ```python
    def teardown(self, handles: dict[str, Any]) -> None:
        return None
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;handles&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
