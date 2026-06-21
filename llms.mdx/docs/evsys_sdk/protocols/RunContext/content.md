# RunContext (/docs/evsys_sdk/protocols/RunContext)



Everything an algorithm needs to execute one training run.

Held loosely on purpose: an algorithm only consumes the fields it needs.
Backends construct this from the parsed ExperimentConfig.

## Attributes [#attributes]

<PyAttribute name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null">
  Stable id for this run; used for output paths and log keys.
</PyAttribute>

<PyAttribute name="&#x22;output_dir&#x22;" type="&#x22;str&#x22;" value="null">
  Local filesystem dir where the run can write checkpoints, logs, etc.
</PyAttribute>

<PyAttribute name="&#x22;config&#x22;" type="&#x22;Any&#x22;" value="null">
  The full parsed ExperimentConfig (kept generic to avoid circular import).
</PyAttribute>

<PyAttribute name="&#x22;data_store&#x22;" type="&#x22;DataStore&#x22;" value="null">
  Datastore handle (read inputs, write outputs).
</PyAttribute>

<PyAttribute name="&#x22;log_store&#x22;" type="&#x22;LogStore&#x22;" value="null">
  Log store (metrics, scalars, artifacts).
</PyAttribute>

<PyAttribute name="&#x22;backend&#x22;" type="&#x22;Backend&#x22;" value="null">
  Backend handle (Tinker / Local / Mock).
</PyAttribute>

<PyAttribute name="&#x22;extras&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;field(default_factory=dict)&#x22;">
  Free-form bag for backend-specific bits (e.g. tinker training\_client).
</PyAttribute>

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, run_id, output_dir, config, data_store, log_store, backend, extras=dict()) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;output_dir&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;config&#x22;" type="&#x22;Any&#x22;" value="null" />

    <PyParameter name="&#x22;data_store&#x22;" type="&#x22;DataStore&#x22;" value="null" />

    <PyParameter name="&#x22;log_store&#x22;" type="&#x22;LogStore&#x22;" value="null" />

    <PyParameter name="&#x22;backend&#x22;" type="&#x22;Backend&#x22;" value="null" />

    <PyParameter name="&#x22;extras&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;dict()&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
