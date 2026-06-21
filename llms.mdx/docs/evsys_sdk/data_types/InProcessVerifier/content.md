# InProcessVerifier (/docs/evsys_sdk/data_types/InProcessVerifier)



Cheap Python-function verifier.

The runner looks up `fn_name` in a registered verifier-fn map and calls
it with `(completion, expected, **params)`. Sub-millisecond — use this
for tool-call matching, exact-match, boxed-answer parsing.

## Attributes [#attributes]

<PyAttribute name="&#x22;fn_name&#x22;" type="&#x22;str&#x22;" value="null" />

<PyAttribute name="&#x22;expected&#x22;" type="&#x22;Any&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;params&#x22;" type="&#x22;dict&#x22;" value="&#x22;field(default_factory=dict)&#x22;" />

<PyAttribute name="&#x22;kind&#x22;" type="&#x22;Literal['in_process']&#x22;" value="&#x22;'in_process'&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, fn_name, expected=None, params=dict(), kind='in_process') -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;fn_name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;expected&#x22;" type="&#x22;Any&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;params&#x22;" type="&#x22;dict&#x22;" value="&#x22;dict()&#x22;" />

    <PyParameter name="&#x22;kind&#x22;" type="&#x22;Literal['in_process']&#x22;" value="&#x22;'in_process'&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
