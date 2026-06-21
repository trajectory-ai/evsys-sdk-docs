# checkpoints (/docs/evsys_sdk/training/checkpoints)



CheckpointManager — write the `checkpoints.jsonl` manifest that
:mod:`evsys_sdk.checkpoint` already knows how to read.

The reader half lives in `evsys_sdk/checkpoint.py`
(:func:`~evsys_sdk.checkpoint.read_manifest`,
:meth:`~evsys_sdk.checkpoint.Checkpoint.pick_final`). This is the writer
counterpart used by :class:`~evsys_sdk.training.loop.TrainingLoop`.

The format is intentionally identical to the manifest tinker\_cookbook used
to emit, so downstream consumers (e.g.
:meth:`~evsys_sdk.inference.tinker.TinkerInference.from_run_result`) keep
working unchanged when an algorithm is ported to the native loop.

Schema per row (one JSON object per line)::

\{"name": "step\_500", "batch": 499, "epoch": 3,
"state\_path": "tinker://.../weights/step\_500",
"sampler\_path": "tinker://.../sampler\_weights/step\_500"}

`state_path` is the full training state (weights + optimizer) used for
resume. `sampler_path` is the inference-ready weights snapshot used by
the eval client.

<PyAttribute name="&#x22;logger&#x22;" type="null" value="&#x22;logging.getLogger(__name__)&#x22;" />

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['CheckpointManager', 'ManifestRow']&#x22;" />

<Tabs items="[&#x22;Class&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;ManifestRow&#x22;" href="&#x22;/docs/evsys_sdk/training/checkpoints/ManifestRow&#x22;" />

      <Card title="&#x22;CheckpointManager&#x22;" href="&#x22;/docs/evsys_sdk/training/checkpoints/CheckpointManager&#x22;" />
    </Cards>
  </Tab>
</Tabs>
