# training (/docs/evsys_sdk/training)



evsys\_sdk.training — native training-loop package.

Three concerns, three modules:

* :mod:`~evsys_sdk.training.backend` — `Backend` Protocol, `SamplingClient`
  Protocol, in-memory `MockBackend` for tests. The only seam that talks to
  tinker (or a stand-in).
* :mod:`~evsys_sdk.training.loop` — `TrainingLoop` driver, `StepBuilder`
  Protocol, `TrainingBatch` dataclass, `Evaluator` Protocol, `LoopArtifacts`.
* :mod:`~evsys_sdk.training.checkpoints` — `CheckpointManager` (writes the
  manifest :class:`evsys_sdk.checkpoint.Checkpoint` reads).

Concrete algorithm wrappers (`sft`, `sdft`, `rl`) live
under :mod:`evsys_sdk.algorithms` and compose these three pieces; researchers
who want a custom algorithm can subclass `StepBuilder` and re-register.

See `docs/DESIGN.md` for the architecture overview, and the
"Writing a new algorithm" section in `skills/using-the-sdk/SKILL.md` for
a template.

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['Backend', 'BenchmarkEvaluator', 'Callback', 'CheckpointManager', 'CsvMetricsCallback', 'EarlyStoppingCallback', 'LogContext', 'LoopState', 'PrintProgressCallback', 'Evaluator', 'ForwardBackwardResult', 'LoopArtifacts', 'LossCallable', 'ManifestRow', 'Message', 'MockBackend', 'MockSamplingClient', 'OptimStepResult', 'DatumMetadata', 'SDFTDataset', 'SamplingClient', 'SimpleSDFTDataset', 'StepBuilder', 'Trajectory', 'TrajectoryGroup', 'assemble_training_data', 'build_callbacks', 'dispatch', 'coerce_floats', 'extract_completion_tokens_from_response', 'extract_weights', 'build_in_loop_evaluators', 'compute_advantages', 'compute_trajectory_metrics', 'TinkerBackend', 'TinkerSamplingClient', 'TrainingBatch', 'TrainingLoop', 'apply_template', 'messages_to_model_input', 'row_to_datum', 'sft_tokenize', 'text_to_model_input']&#x22;" />

<Tabs items="[&#x22;Modules&#x22;]">
  <Tab value="&#x22;Modules&#x22;">
    <Cards>
      <Card href="&#x22;/docs/evsys_sdk/training/backend&#x22;" title="&#x22;backend&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/training/harbor_eval&#x22;" title="&#x22;harbor_eval&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/training/trajectory&#x22;" title="&#x22;trajectory&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/training/batch_utils&#x22;" title="&#x22;batch_utils&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/training/harbor_agents&#x22;" title="&#x22;harbor_agents&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/training/checkpoints&#x22;" title="&#x22;checkpoints&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/training/sft_data&#x22;" title="&#x22;sft_data&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/training/templates&#x22;" title="&#x22;templates&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/training/harbor_engine&#x22;" title="&#x22;harbor_engine&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/training/tinker_backend&#x22;" title="&#x22;tinker_backend&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/training/callbacks&#x22;" title="&#x22;callbacks&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/training/data_processing&#x22;" title="&#x22;data_processing&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/training/loop&#x22;" title="&#x22;loop&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/training/sdft_data&#x22;" title="&#x22;sdft_data&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/training/evaluators&#x22;" title="&#x22;evaluators&#x22;" />
    </Cards>
  </Tab>
</Tabs>
