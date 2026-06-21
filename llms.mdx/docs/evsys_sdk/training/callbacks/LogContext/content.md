# LogContext (/docs/evsys_sdk/training/callbacks/LogContext)



Mutable context threaded to every callback hook, in BOTH the
Experiment scope (lifecycle / benchmark eval) and the TrainingLoop scope
(per-step / checkpoint). Logger callbacks *create* the dashboard ids and
write them onto :attr:`ids`; later hooks read them back.

The framework owns only :attr:`run_key` (the local output-dir name). All
dashboard ids (`experiment_id` / `group:\<name>` / `run_id`) are
callback-owned and accumulate on :attr:`ids` as the `create_*` hooks
fire. Arms run sequentially, so `ids["run_id"]` is the current arm's.

## Attributes [#attributes]

<PyAttribute name="&#x22;output_dir&#x22;" type="&#x22;Path&#x22;" value="null" />

<PyAttribute name="&#x22;config&#x22;" type="&#x22;'ExperimentConfig | None'&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;store&#x22;" type="&#x22;Any&#x22;" value="&#x22;None&#x22;">
  Resolved store handle (EvsysStore / LocalStore / DashboardClient) or
  None when no store is configured.
</PyAttribute>

<PyAttribute name="&#x22;run_key&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;run_config&#x22;" type="&#x22;'RunConfig | None'&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;group_name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;ids&#x22;" type="&#x22;dict[str, str]&#x22;" value="&#x22;field(default_factory=dict)&#x22;">
  Callback-populated dashboard ids: `experiment_id`, `group:\<name>`,
  `run_id`. Read parents here to link children (e.g. create\_run reads
  `ids['experiment_id']`).
</PyAttribute>

<PyAttribute name="&#x22;extras&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;field(default_factory=dict)&#x22;">
  Scratch for passing values between callbacks within a run (e.g.
  `wandb_url` set by wandb\_logger, read by evsys\_logger's create\_run).
</PyAttribute>

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, output_dir, config=None, store=None, run_key=None, run_config=None, group_name=None, ids=dict(), extras=dict()) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;output_dir&#x22;" type="&#x22;Path&#x22;" value="null" />

    <PyParameter name="&#x22;config&#x22;" type="&#x22;'ExperimentConfig | None'&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;store&#x22;" type="&#x22;Any&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;run_key&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;run_config&#x22;" type="&#x22;'RunConfig | None'&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;group_name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;ids&#x22;" type="&#x22;dict[str, str]&#x22;" value="&#x22;dict()&#x22;" />

    <PyParameter name="&#x22;extras&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;dict()&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
