# harbor_agents (/docs/evsys_sdk/training/harbor_agents)



Harbor glue classes — agent + environment (harbor 0.13.2).

These subclass harbor base classes, so `harbor` is imported at module top.
Our own code **never imports this module directly** — harbor loads these by the
string `import_path` recorded in the job/agent config at runtime (see
:mod:`evsys_sdk.training.harbor_engine`). That keeps the optional `[harbor]`
extra out of the base import path.

* :class:`NoOpEnvironment` — sandbox-free `BaseEnvironment` (no container).
* :class:`BasicLoopAgent` — drives `Chat(\<llm>)` on-policy, records token-level
  `rollout_details` onto the `AgentContext`, and writes the completion to the
  agent dir so the verifier can read it. The default agent. `model_client` picks
  the sampler: `"tinker"` (on-policy `TinkerLLM`) or `"litellm"` (any provider
  litellm supports, for benchmarking closed/API models through the same path).
  Users can also plug any `BaseAgent` via `agent_import_path`.
* :class:`EvsysVerifier` — a harbor `BaseVerifier` that wraps our **registered
  verifier fns**. It runs host-side (no container exec): reads the completion the
  agent wrote + the per-task spec (`fn_name`/`expected`/`params`)
  materialized into the task dir, and returns the reward as a harbor
  `VerifierResult`. Used in SHARED mode (a dummy `tests/test.sh`, written by
  the materializer, satisfies harbor's task-load check and is never executed).

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['BasicLoopAgent', 'EvsysVerifier', 'NoOpEnvironment']&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;NoOpEnvironment&#x22;" href="&#x22;/docs/evsys_sdk/training/harbor_agents/NoOpEnvironment&#x22;" />

      <Card title="&#x22;BasicLoopAgent&#x22;" href="&#x22;/docs/evsys_sdk/training/harbor_agents/BasicLoopAgent&#x22;" />

      <Card title="&#x22;EvsysVerifier&#x22;" href="&#x22;/docs/evsys_sdk/training/harbor_agents/EvsysVerifier&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;_read_text&#x22;" type="&#x22;(path) -> str&#x22;">
      <PySourceCode>
        ```python
        def _read_text(path: Path) -> str:
            try:
                return path.read_text() if path.exists() else ""
            except Exception:  # pragma: no cover - defensive
                return ""
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;path&#x22;" type="&#x22;Path&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;str&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_read_json&#x22;" type="&#x22;(path) -> dict[str, Any]&#x22;">
      <PySourceCode>
        ```python
        def _read_json(path: Path) -> dict[str, Any]:
            try:
                return json.loads(path.read_text()) if path.exists() else {}
            except Exception:  # pragma: no cover - defensive
                return {}
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;path&#x22;" type="&#x22;Path&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;dict[str, typing.Any]&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
