# sdft (/docs/evsys_sdk/algorithms/sdft)



SDFT — self-distillation fine-tuning on the SDK training loop.

All the
composer plumbing lives in
:class:`~evsys_sdk.algorithms.base.BaseAlgorithm`; SDFT supplies the two
per-algorithm pieces:

* :meth:`setup` — parse rows → :class:`PromptExample` dataset, build the
  frozen teacher sampling client, and stash a per-step *student sampler
  provider* (snapshots the current weights each step so rollouts stay
  on-policy — cookbook parity without monkey-patching `sdft.train_step`).
* :meth:`build_batch` — on-policy student rollout → teacher topK score → CE
  distillation Datums. :meth:`step_metrics` adds `train/mean_loss` from the
  forward-backward output.

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['SDFT', 'SDFTConfig']&#x22;" />

<Tabs items="[&#x22;Class&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;SDFTConfig&#x22;" href="&#x22;/docs/evsys_sdk/algorithms/sdft/SDFTConfig&#x22;" />

      <Card title="&#x22;SDFT&#x22;" href="&#x22;/docs/evsys_sdk/algorithms/sdft/SDFT&#x22;" />
    </Cards>
  </Tab>
</Tabs>
