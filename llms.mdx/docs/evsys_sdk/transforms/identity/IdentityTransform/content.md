# IdentityTransform (/docs/evsys_sdk/transforms/identity/IdentityTransform)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'identity'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;IdentityConfig&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__call__&#x22;" type="&#x22;(self, rows) -> Iterable[dict[str, Any]]&#x22;">
  <PySourceCode>
    ```python
    def __call__(self, rows: Iterable[dict[str, Any]]) -> Iterable[dict[str, Any]]:
        return iter(rows)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;rows&#x22;" type="&#x22;Iterable[dict[str, Any]]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Iterable[dict[str, typing.Any]]&#x22;" />
</PyFunction>
