# Algorithm (/docs/evsys_sdk/protocols/Algorithm)



A training recipe.

Implementations must declare:

* `name` (class var) — registry key, also used as the YAML `kind`.
* `Config` (class var) — Pydantic model for the recipe's parameters.

And implement:

* `train(ctx: RunContext) -> RunResult`

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="null" />

## Functions [#functions]

<PyFunction name="&#x22;train&#x22;" type="&#x22;(self, ctx) -> RunResult&#x22;">
  <PySourceCode>
    ```python
    def train(self, ctx: RunContext) -> RunResult: ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;RunContext&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.protocols.RunResult&#x22;" />
</PyFunction>
