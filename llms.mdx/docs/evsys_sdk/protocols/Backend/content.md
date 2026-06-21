# Backend (/docs/evsys_sdk/protocols/Backend)



Compute backend.

A backend's job is to materialise a model + training resources and hand
them to whichever algorithm runs. The Algorithm protocol is intentionally
NOT parameterised by Backend: instead, the registry routes (recipe.kind,
backend.kind) to a specific Algorithm implementation.

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

## Functions [#functions]

<PyFunction name="&#x22;prepare&#x22;" type="&#x22;(self, *, model, run_dir) -> dict[str, Any]&#x22;">
  Return a dict of backend-specific handles attached to ctx.extras.

  e.g. for TinkerBackend: \{'service\_client': ..., 'training\_client': ...,
  'tokenizer': ..., 'model\_name': ...}.

  <PySourceCode>
    ```python
    def prepare(self, *, model: dict[str, Any], run_dir: str) -> dict[str, Any]:
        """Return a dict of backend-specific handles attached to ctx.extras.

        e.g. for TinkerBackend: {'service_client': ..., 'training_client': ...,
        'tokenizer': ..., 'model_name': ...}.
        """
        ...
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
    def teardown(self, handles: dict[str, Any]) -> None: ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;handles&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
