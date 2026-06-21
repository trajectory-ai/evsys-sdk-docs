# local_store (/docs/evsys_sdk/local_store)



Always-on local mirror of experiment data (wandb-offline style).

Every DashboardClient write is also persisted under `EVSYS_LOG_DIR`
(default `./evsys_sdk`). This guarantees no data is lost even
when the backend is unreachable, and is the *only* store used in offline mode.

Layout (flat by id, so each call only needs its own id)::

\{log\_dir}/
experiments/\{experiment\_id}/experiment.json
generations/\{generation\_id}/generation.json
metrics.jsonl
evals.jsonl
predictions.jsonl

<PyAttribute name="&#x22;log&#x22;" type="null" value="&#x22;get_logger(__name__)&#x22;" />

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['LocalExperimentStore', 'resolve_log_dir']&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;LocalExperimentStore&#x22;" href="&#x22;/docs/evsys_sdk/local_store/LocalExperimentStore&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;resolve_log_dir&#x22;" type="&#x22;(log_dir=None) -> Path&#x22;">
      Resolve the local mirror directory from arg or EVSYS\_LOG\_DIR.

      <PySourceCode>
        ```python
        def resolve_log_dir(log_dir: str | None = None) -> Path:
            """Resolve the local mirror directory from arg or EVSYS_LOG_DIR."""
            raw = log_dir or os.environ.get(EVSYS_LOG_DIR_ENV) or DEFAULT_LOG_DIR
            return Path(raw).expanduser()
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;log_dir&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="&#x22;pathlib.Path&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
