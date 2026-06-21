# TinkerBackend (/docs/evsys_sdk/backends/tinker/TinkerBackend)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'tinker'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;TinkerBackendConfig&#x22;" />

<PyAttribute name="&#x22;api_key_env&#x22;" type="null" value="&#x22;api_key_env&#x22;" />

<PyAttribute name="&#x22;base_url&#x22;" type="null" value="&#x22;base_url&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, api_key_env='TINKER_API_KEY', base_url=None) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, *, api_key_env: str = "TINKER_API_KEY", base_url: str | None = None) -> None:
        self.api_key_env = api_key_env
        self.base_url = base_url
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;api_key_env&#x22;" type="&#x22;str&#x22;" value="&#x22;'TINKER_API_KEY'&#x22;" />

    <PyParameter name="&#x22;base_url&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;prepare&#x22;" type="&#x22;(self, *, model, run_dir) -> dict[str, Any]&#x22;">
  <PySourceCode>
    ```python
    def prepare(self, *, model: dict[str, Any], run_dir: str) -> dict[str, Any]:
        api_key = os.environ.get(self.api_key_env)
        if not api_key:
            raise RuntimeError(
                f"{self.api_key_env} not set in env — needed for TinkerBackend.prepare()"
            )
        kwargs: dict[str, Any] = {}
        if self.base_url:
            kwargs["base_url"] = self.base_url
        # tinker.ServiceClient picks up the API key from env automatically;
        # we set it explicitly so the active env wins over any earlier export.
        os.environ[self.api_key_env] = api_key
        service_client = tinker.ServiceClient(**kwargs)
        return {
            "backend": "tinker",
            "service_client": service_client,
            "model_name": model["name"],
            "load_checkpoint_path": model.get("load_checkpoint_path"),
            "init_from_checkpoint": model.get("init_from_checkpoint"),
            "renderer_name": model.get("renderer_name"),
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
