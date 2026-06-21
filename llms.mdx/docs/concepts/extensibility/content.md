# Extensibility (/docs/concepts/extensibility)



The recurring pattern across the whole SDK: &#x2A;*implement a protocol → register
it under a `kind` → reference it in YAML.** No subclassing the library, no
fork. Researchers register their own with the same decorator in their own
project.

<Mermaid
  chart="flowchart TB
    Y[&#x22;config.yaml<br/>{ kind: grpo, params: {…} }&#x22;] --> F[&#x22;Factory&#x22;]
    F -->|&#x22;get_algorithm(kind)&#x22;| REG[&#x22;Registry&#x22;]
    F -->|&#x22;validate params&#x22;| CFG[&#x22;Class.Config<br/>Pydantic, extra=forbid&#x22;]
    REG --> CLS[&#x22;Implementation class<br/>name + Config ClassVars&#x22;]
    CFG --> CLS
    CLS --> INST[&#x22;Live instance&#x22;]

    subgraph Builtins[&#x22;Built-ins (@register_*)&#x22;]
        A[&#x22;Algorithms&#x22;]
        V[&#x22;Verifiers&#x22;]
        M[&#x22;Metrics&#x22;]
        B[&#x22;Backends&#x22;]
    end
    USER[&#x22;Your project<br/>@register_algorithm('my_algo')&#x22;] -.->|&#x22;same decorator, no SDK edit&#x22;| REG
    Builtins -->|&#x22;register at import&#x22;| REG"
/>

## The four-step contract [#the-four-step-contract]

<Steps>
  <Step>
    ### A registry [#a-registry]

    Each extension *kind* has a `Registry("<kind>")` plus `register_<kind>` /
    `get_<kind>` / `list_<kind>s` helpers.
  </Step>

  <Step>
    ### A class with two ClassVars [#a-class-with-two-classvars]

    Every implementation carries a `name` (the string used in YAML) and a `Config`
    (a Pydantic model, `extra="forbid"`) — decorated with `@register_<kind>("<name>")`.

    ```python
    from pydantic import BaseModel
    from evsys_sdk import register_algorithm

    @register_algorithm("dpo")
    class DPO:
        name = "dpo"

        class Config(BaseModel, extra="forbid"):
            beta: float = 0.1
    ```
  </Step>

  <Step>
    ### A YAML surface [#a-yaml-surface]

    A `<Kind>Spec` model shaped as `kind` + `params`, and a `list[<Kind>Spec]`
    field on whatever config owns it.
  </Step>

  <Step>
    ### A factory [#a-factory]

    Resolves specs into instances: look up the class via `get_<kind>(spec.kind)`,
    validate `spec.params` against the class's `Config`, then construct.
  </Step>
</Steps>

## The eight registries [#the-eight-registries]

One registry per extension point. The YAML `kind` resolves into it.

| Registry          | Decorator              | `kind` used in                            |
| ----------------- | ---------------------- | ----------------------------------------- |
| algorithm         | `@register_algorithm`  | `run.algorithm.kind`                      |
| backend           | `@register_backend`    | `run.backend.kind`                        |
| transform         | `@register_transform`  | `data.transforms[].kind`                  |
| data\_store       | `@register_data_store` | `data_store.kind`                         |
| log\_store        | `@register_log_store`  | `log_store.kind`                          |
| metric            | `@register_metric`     | `eval.metrics[]` / `validation.metrics[]` |
| verifier          | `@register_verifier`   | verifier specs / RL reward                |
| inference\_client | `@register_inference`  | `eval.inference.kind`                     |

## The payoff [#the-payoff]

Enable a feature from `config.yaml`:

```yaml
algorithm:
  kind: dpo
  params:
    beta: 0.1
```

…and register your own with the same decorator — or ship it in a third-party
package via entry points (group `evsys_sdk.<plural>`). The whole surface stays
predictable.

See the [registry API](/docs/evsys_sdk/registry) and the
[SDK Reference](/docs/reference) for every built-in kind.
