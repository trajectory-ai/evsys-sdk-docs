# combo (/docs/evsys_sdk/algorithms/combo)



Combo — chain multiple algorithms sequentially in one run.

Use case: `SFT warmup → GRPO refine`, `cold-start distill → DAPO`, etc.
Each phase runs against the same RunContext but writes its artifacts to a
`phase\{i\}_\{name\}` subdirectory; the previous phase's `final_checkpoint`
artifact is threaded into the next phase via `ctx.extras`.

YAML example::

kind: combo
phases:

* kind: mock\_sft
  config:
  num\_epochs: 1
* kind: mock\_rl
  config:
  num\_iterations: 50

<PyAttribute name="&#x22;logger&#x22;" type="null" value="&#x22;logging.getLogger(__name__)&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;ComboPhaseConfig&#x22;" href="&#x22;/docs/evsys_sdk/algorithms/combo/ComboPhaseConfig&#x22;" />

      <Card title="&#x22;ComboConfig&#x22;" href="&#x22;/docs/evsys_sdk/algorithms/combo/ComboConfig&#x22;" />

      <Card title="&#x22;ComboAlgorithm&#x22;" href="&#x22;/docs/evsys_sdk/algorithms/combo/ComboAlgorithm&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;_phase_context&#x22;" type="&#x22;(parent, phase_dir, prior_artifacts, label) -> RunContext&#x22;">
      Build a per-phase RunContext that:

      * writes into phase\_dir
      * exposes the previous phase's final\_checkpoint via ctx.extras
      * shares the parent's data\_store, log\_store, backend

      <PySourceCode>
        ```python
        def _phase_context(
            parent: RunContext,
            phase_dir: Path,
            prior_artifacts: dict[str, str],
            label: str,
        ) -> RunContext:
            """Build a per-phase RunContext that:
                - writes into phase_dir
                - exposes the previous phase's final_checkpoint via ctx.extras
                - shares the parent's data_store, log_store, backend
            """
            new_extras = dict(parent.extras)
            # Thread the most recent final_checkpoint forward as `init_checkpoint`.
            for k, v in reversed(list(prior_artifacts.items())):
                if k.endswith("/final_checkpoint") or k == "final_checkpoint":
                    new_extras["init_checkpoint"] = v
                    break
            new_extras["combo_phase"] = label

            return replace(
                parent,
                run_id=f"{parent.run_id}/{label}",
                output_dir=str(phase_dir),
                extras=new_extras,
            ) if dataclasses.is_dataclass(parent) else parent  # type: ignore[arg-type]
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;parent&#x22;" type="&#x22;RunContext&#x22;" value="null" />

        <PyParameter name="&#x22;phase_dir&#x22;" type="&#x22;Path&#x22;" value="null" />

        <PyParameter name="&#x22;prior_artifacts&#x22;" type="&#x22;dict[str, str]&#x22;" value="null" />

        <PyParameter name="&#x22;label&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;evsys_sdk.protocols.RunContext&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
