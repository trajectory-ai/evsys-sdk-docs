# sft (/docs/evsys_sdk/algorithms/sft)



SFT — supervised fine-tuning on the SDK training loop.

All the composer
plumbing (backend, step/save cadence, evaluators, loop wiring, artifacts)
lives in :class:`~evsys_sdk.algorithms.base.BaseAlgorithm`. SFT only has to
say *how a batch is built*:

* :meth:`setup` — standardize rows → typed :class:`ChatMessagesRow`, tokenize
  to a static list of :class:`tinker.Datum` with assistant-span loss masks.
* :meth:`build_batch` — slice `batch_size` datums per step (wrapping the
  dataset so `num_steps` can exceed one epoch).
* :meth:`step_metrics` — `train_mean_nll` from the per-position logprobs.

Researchers wanting a one-line tweak (focal loss, custom loss, extra metrics)
subclass `SFT` and override `build_batch` / `step_metrics` — no SDK
change needed.

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['SFT', 'SFTConfig']&#x22;" />

<Tabs items="[&#x22;Class&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;SFTConfig&#x22;" href="&#x22;/docs/evsys_sdk/algorithms/sft/SFTConfig&#x22;" />

      <Card title="&#x22;SFT&#x22;" href="&#x22;/docs/evsys_sdk/algorithms/sft/SFT&#x22;" />
    </Cards>
  </Tab>
</Tabs>
