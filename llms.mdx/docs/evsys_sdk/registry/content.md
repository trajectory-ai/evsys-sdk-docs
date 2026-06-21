# registry (/docs/evsys_sdk/registry)



Registries — one per extension point.

Each registry is a tiny namespace with:

* `register_\<thing>(name)` decorator.
* `get_\<thing>(name)` lookup.
* `list_\<thing>s()` enumeration.

Adding a new algorithm/verifier/metric is one decorator + a Pydantic Config
class. No subclassing the library, no editing any list.

Third-party packages can also register via Python entry points; see
`_entry_points.py` for the loader.

<PyAttribute name="&#x22;T&#x22;" type="null" value="&#x22;TypeVar('T')&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;Registry&#x22;" href="&#x22;/docs/evsys_sdk/registry/Registry&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;register_algorithm&#x22;" type="&#x22;(name=None)&#x22;">
      <PySourceCode>
        ```python
        def register_algorithm(name: str | None = None):
            return _algorithms.register(name)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="null" />
    </PyFunction>

    <PyFunction name="&#x22;register_verifier&#x22;" type="&#x22;(name=None)&#x22;">
      <PySourceCode>
        ```python
        def register_verifier(name: str | None = None):
            return _verifiers.register(name)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="null" />
    </PyFunction>

    <PyFunction name="&#x22;register_metric&#x22;" type="&#x22;(name=None)&#x22;">
      <PySourceCode>
        ```python
        def register_metric(name: str | None = None):
            return _metrics.register(name)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="null" />
    </PyFunction>

    <PyFunction name="&#x22;register_data_store&#x22;" type="&#x22;(name=None)&#x22;">
      <PySourceCode>
        ```python
        def register_data_store(name: str | None = None):
            return _data_stores.register(name)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="null" />
    </PyFunction>

    <PyFunction name="&#x22;register_log_store&#x22;" type="&#x22;(name=None)&#x22;">
      <PySourceCode>
        ```python
        def register_log_store(name: str | None = None):
            return _log_stores.register(name)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="null" />
    </PyFunction>

    <PyFunction name="&#x22;register_backend&#x22;" type="&#x22;(name=None)&#x22;">
      <PySourceCode>
        ```python
        def register_backend(name: str | None = None):
            return _backends.register(name)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="null" />
    </PyFunction>

    <PyFunction name="&#x22;register_inference&#x22;" type="&#x22;(name=None)&#x22;">
      <PySourceCode>
        ```python
        def register_inference(name: str | None = None):
            return _inference.register(name)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="null" />
    </PyFunction>

    <PyFunction name="&#x22;register_transform&#x22;" type="&#x22;(name=None)&#x22;">
      <PySourceCode>
        ```python
        def register_transform(name: str | None = None):
            return _transforms.register(name)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="null" />
    </PyFunction>

    <PyFunction name="&#x22;register_callback&#x22;" type="&#x22;(name=None)&#x22;">
      <PySourceCode>
        ```python
        def register_callback(name: str | None = None):
            return _callbacks.register(name)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="null" />
    </PyFunction>

    <PyFunction name="&#x22;register_default_inference_factory&#x22;" type="&#x22;(backend_kind) -> Callable[[Callable[..., Any]], Callable[..., Any]]&#x22;">
      Register the default `(run_result, run_cfg) -> InferenceClient` for
      a backend kind. Called by each inference module's import side-effect
      (e.g. `inference/tinker.py` registers `"tinker"`).

      <PySourceCode>
        ```python
        def register_default_inference_factory(backend_kind: str) -> Callable[[Callable[..., Any]], Callable[..., Any]]:
            """Register the default ``(run_result, run_cfg) -> InferenceClient`` for
            a backend kind. Called by each inference module's import side-effect
            (e.g. ``inference/tinker.py`` registers ``"tinker"``).
            """
            def deco(fn: Callable[..., Any]) -> Callable[..., Any]:
                _DEFAULT_INFERENCE_FACTORIES[backend_kind] = fn
                return fn
            return deco
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;backend_kind&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;typing.Callable[[typing.Callable[..., typing.Any]], typing.Callable[..., typing.Any]]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;get_algorithm&#x22;" type="&#x22;(name) -> type&#x22;">
      <PySourceCode>
        ```python
        def get_algorithm(name: str) -> type:
            return _algorithms.get(name)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;type&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;get_verifier&#x22;" type="&#x22;(name) -> type&#x22;">
      <PySourceCode>
        ```python
        def get_verifier(name: str) -> type:
            return _verifiers.get(name)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;type&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;get_metric&#x22;" type="&#x22;(name) -> type&#x22;">
      <PySourceCode>
        ```python
        def get_metric(name: str) -> type:
            return _metrics.get(name)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;type&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;get_data_store&#x22;" type="&#x22;(name) -> type&#x22;">
      <PySourceCode>
        ```python
        def get_data_store(name: str) -> type:
            return _data_stores.get(name)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;type&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;get_log_store&#x22;" type="&#x22;(name) -> type&#x22;">
      <PySourceCode>
        ```python
        def get_log_store(name: str) -> type:
            return _log_stores.get(name)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;type&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;get_backend&#x22;" type="&#x22;(name) -> type&#x22;">
      <PySourceCode>
        ```python
        def get_backend(name: str) -> type:
            return _backends.get(name)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;type&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;get_inference&#x22;" type="&#x22;(name) -> type&#x22;">
      <PySourceCode>
        ```python
        def get_inference(name: str) -> type:
            return _inference.get(name)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;type&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;get_transform&#x22;" type="&#x22;(name) -> type&#x22;">
      <PySourceCode>
        ```python
        def get_transform(name: str) -> type:
            return _transforms.get(name)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;type&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;get_callback&#x22;" type="&#x22;(name) -> type&#x22;">
      <PySourceCode>
        ```python
        def get_callback(name: str) -> type:
            return _callbacks.get(name)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;type&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;get_default_inference_factory&#x22;" type="&#x22;(backend_kind) -> Callable[..., Any] | None&#x22;">
      Return the registered default factory for `backend_kind` or `None`.

      <PySourceCode>
        ```python
        def get_default_inference_factory(backend_kind: str) -> Callable[..., Any] | None:
            """Return the registered default factory for ``backend_kind`` or ``None``."""
            return _DEFAULT_INFERENCE_FACTORIES.get(backend_kind)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;backend_kind&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;typing.Callable[..., typing.Any] | None&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;list_algorithms&#x22;" type="&#x22;() -> list[str]&#x22;">
      <PySourceCode>
        ```python
        def list_algorithms() -> list[str]:
            return _algorithms.list()
        ```
      </PySourceCode>

      <PyFunctionReturn type="&#x22;list[str]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;list_verifiers&#x22;" type="&#x22;() -> list[str]&#x22;">
      <PySourceCode>
        ```python
        def list_verifiers() -> list[str]:
            return _verifiers.list()
        ```
      </PySourceCode>

      <PyFunctionReturn type="&#x22;list[str]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;list_metrics&#x22;" type="&#x22;() -> list[str]&#x22;">
      <PySourceCode>
        ```python
        def list_metrics() -> list[str]:
            return _metrics.list()
        ```
      </PySourceCode>

      <PyFunctionReturn type="&#x22;list[str]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;list_data_stores&#x22;" type="&#x22;() -> list[str]&#x22;">
      <PySourceCode>
        ```python
        def list_data_stores() -> list[str]:
            return _data_stores.list()
        ```
      </PySourceCode>

      <PyFunctionReturn type="&#x22;list[str]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;list_log_stores&#x22;" type="&#x22;() -> list[str]&#x22;">
      <PySourceCode>
        ```python
        def list_log_stores() -> list[str]:
            return _log_stores.list()
        ```
      </PySourceCode>

      <PyFunctionReturn type="&#x22;list[str]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;list_backends&#x22;" type="&#x22;() -> list[str]&#x22;">
      <PySourceCode>
        ```python
        def list_backends() -> list[str]:
            return _backends.list()
        ```
      </PySourceCode>

      <PyFunctionReturn type="&#x22;list[str]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;list_inferences&#x22;" type="&#x22;() -> list[str]&#x22;">
      <PySourceCode>
        ```python
        def list_inferences() -> list[str]:
            return _inference.list()
        ```
      </PySourceCode>

      <PyFunctionReturn type="&#x22;list[str]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;list_transforms&#x22;" type="&#x22;() -> list[str]&#x22;">
      <PySourceCode>
        ```python
        def list_transforms() -> list[str]:
            return _transforms.list()
        ```
      </PySourceCode>

      <PyFunctionReturn type="&#x22;list[str]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;list_callbacks&#x22;" type="&#x22;() -> list[str]&#x22;">
      <PySourceCode>
        ```python
        def list_callbacks() -> list[str]:
            return _callbacks.list()
        ```
      </PySourceCode>

      <PyFunctionReturn type="&#x22;list[str]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_all_registries&#x22;" type="&#x22;() -> dict[str, Registry]&#x22;">
      <PySourceCode>
        ```python
        def _all_registries() -> dict[str, Registry]:
            return {
                "algorithm": _algorithms,
                "verifier": _verifiers,
                "metric": _metrics,
                "data_store": _data_stores,
                "log_store": _log_stores,
                "backend": _backends,
                "inference_client": _inference,
                "transform": _transforms,
                "callback": _callbacks,
            }
        ```
      </PySourceCode>

      <PyFunctionReturn type="&#x22;dict[str, evsys_sdk.registry.Registry]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;schema_for&#x22;" type="&#x22;(kind, name) -> dict[str, Any]&#x22;">
      Return the JSON schema for a registered extension's Config.

      Used by evolutionary algorithms to know the legal field space.

      <PySourceCode>
        ```python
        def schema_for(kind: str, name: str) -> dict[str, Any]:
            """Return the JSON schema for a registered extension's Config.

            Used by evolutionary algorithms to know the legal field space.
            """
            reg = _all_registries().get(kind)
            if reg is None:
                raise KeyError(f"Unknown registry kind: {kind}")
            cls = reg.get(name)
            cfg_cls = getattr(cls, "Config", None)
            if cfg_cls is None or not hasattr(cfg_cls, "model_json_schema"):
                return {"type": "object", "properties": {}}
            return cfg_cls.model_json_schema()
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;kind&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;dict[str, typing.Any]&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
