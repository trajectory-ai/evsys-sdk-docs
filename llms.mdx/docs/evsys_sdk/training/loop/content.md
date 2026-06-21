# loop (/docs/evsys_sdk/training/loop)



TrainingLoop — algorithm-agnostic driver around a tinker
training client.

The loop owns:

* the `for step in range(start, num_steps)` iteration,
* dispatching `forward_backward_async` then `optim_step_async`,
* routing a callable `loss_fn` through `forward_backward_custom_async`,
* periodic checkpoint saves via :class:`~evsys_sdk.training.checkpoints.CheckpointManager`,
* periodic in-loop evaluation via :class:`Evaluator` objects,
* writing one row per step into `ctx.log_store` (so the existing
  `forward_step_metrics` forwarder picks them up unchanged),
* a final "final" checkpoint at the end of training.

What the loop does NOT own: data shaping. A :class:`StepBuilder` decides
what each batch contains and computes algorithm-specific metrics. That's
the seam SFT / SDFT / RL plug into.

Designed to be exercised against
:class:`~evsys_sdk.training.backend.MockBackend` end-to-end so tests don't
need a real tinker session.

<PyAttribute name="&#x22;logger&#x22;" type="null" value="&#x22;logging.getLogger(__name__)&#x22;" />

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['Evaluator', 'LoopArtifacts', 'StepBuilder', 'TrainingBatch', 'TrainingLoop']&#x22;" />

<Tabs items="[&#x22;Class&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;TrainingBatch&#x22;" href="&#x22;/docs/evsys_sdk/training/loop/TrainingBatch&#x22;" />

      <Card title="&#x22;StepBuilder&#x22;" href="&#x22;/docs/evsys_sdk/training/loop/StepBuilder&#x22;" />

      <Card title="&#x22;Evaluator&#x22;" href="&#x22;/docs/evsys_sdk/training/loop/Evaluator&#x22;" />

      <Card title="&#x22;LoopArtifacts&#x22;" href="&#x22;/docs/evsys_sdk/training/loop/LoopArtifacts&#x22;" />

      <Card title="&#x22;_LoopMetricKeys&#x22;" href="&#x22;/docs/evsys_sdk/training/loop/_LoopMetricKeys&#x22;" />

      <Card title="&#x22;TrainingLoop&#x22;" href="&#x22;/docs/evsys_sdk/training/loop/TrainingLoop&#x22;" />
    </Cards>
  </Tab>
</Tabs>
