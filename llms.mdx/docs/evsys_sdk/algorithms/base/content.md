# base (/docs/evsys_sdk/algorithms/base)



BaseAlgorithm — shared base for the SDK's training algorithms.

Every training algorithm (`sft` / `sdft` / `rl`) used to copy-paste the
same composer body: allocate the tinker backend,
resolve the step count + save cadence, log hyperparams, build the in-loop
evaluators, wire a :class:`~evsys_sdk.training.loop.TrainingLoop`, run it, and
record the checkpoint artifacts. The only thing that genuinely differed was
*how each step's batch is built*. This base owns all of the shared plumbing;
a concrete algorithm overrides just the per-algorithm pieces.

The base **is itself a StepBuilder** — it implements `build_batch` and
`step_metrics` and hands `self` to the loop, so there is no separate
StepBuilder object to construct. A subclass overrides:

* :meth:`setup` — one-time prep before the loop. Stash per-algorithm state on
  `self` (tokenized datums for SFT; dataset + teacher client + sampler
  provider for SDFT/RL) and set `self._steps_per_epoch`.
* :meth:`build_batch` — produce the :class:`TrainingBatch` for one step. The
  loss spec rides on the returned batch (`loss_fn` is not a separate
  override). SFT slices its static datums; RL/SDFT roll out on-policy here.
* :meth:`step_metrics&#x60; — &#x2A;(optional)* per-step metrics from the
  forward-backward result. Defaults to `\{\}`.

Researchers wanting a one-line tweak (focal loss, an extra metric, a custom
loss) subclass the concrete algorithm and override `build_batch` /
`step_metrics` — no SDK change needed.

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['BaseAlgorithm', 'BaseAlgorithmConfig']&#x22;" />

<Tabs items="[&#x22;Class&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;BaseAlgorithmConfig&#x22;" href="&#x22;/docs/evsys_sdk/algorithms/base/BaseAlgorithmConfig&#x22;" />

      <Card title="&#x22;BaseAlgorithm&#x22;" href="&#x22;/docs/evsys_sdk/algorithms/base/BaseAlgorithm&#x22;" />
    </Cards>
  </Tab>
</Tabs>
