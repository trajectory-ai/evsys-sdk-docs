# E2BVerifier (/docs/evsys_sdk/data_types/E2BVerifier)



Sandboxed code verifier — runs `test_state.py` inside an E2B container.

The runner builds the container per the `dockerfile`, drops in the
model's completion + `test_state.py`, executes `test_sh` (default
`pytest -q test_state.py`), and reads pass/fail from the exit code.

## Attributes [#attributes]

<PyAttribute name="&#x22;dockerfile&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

<PyAttribute name="&#x22;test_sh&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

<PyAttribute name="&#x22;test_state_py&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

<PyAttribute name="&#x22;kind&#x22;" type="&#x22;Literal['e2b']&#x22;" value="&#x22;'e2b'&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, dockerfile='', test_sh='', test_state_py='', kind='e2b') -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;dockerfile&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

    <PyParameter name="&#x22;test_sh&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

    <PyParameter name="&#x22;test_state_py&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

    <PyParameter name="&#x22;kind&#x22;" type="&#x22;Literal['e2b']&#x22;" value="&#x22;'e2b'&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
