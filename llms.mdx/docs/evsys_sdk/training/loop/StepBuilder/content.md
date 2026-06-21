# StepBuilder (/docs/evsys_sdk/training/loop/StepBuilder)



The only thing each algorithm overrides.

Owns data shaping + per-step metrics. Owns nothing about
checkpointing / logging / eval / resume.

## Functions [#functions]

<PyFunction name="&#x22;build_batch&#x22;" type="&#x22;(self, step_idx) -> TrainingBatch&#x22;">
  Produce the batch for `step_idx` (0-based).

  <PySourceCode>
    ```python
    async def build_batch(self, step_idx: int) -> TrainingBatch:
        """Produce the batch for ``step_idx`` (0-based)."""
        ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;step_idx&#x22;" type="&#x22;int&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.training.loop.TrainingBatch&#x22;" />
</PyFunction>

<PyFunction name="&#x22;step_metrics&#x22;" type="&#x22;(self, step_idx, batch, fb_result) -> dict[str, float]&#x22;">
  Compute per-step metrics from the forward-backward result.

  `fb_result` is what the backend's
  `forward_backward_*_async()` future resolved to. For
  `MockBackend` it's a duck-typed
  :class:`~evsys_sdk.training.backend.ForwardBackwardResult`; for
  `TinkerBackend` it's the native `tinker.ForwardBackwardOutput`.
  The StepBuilder is free to inspect `fb_result.loss_fn_outputs`
  (one entry per Datum).

  <PySourceCode>
    ```python
    def step_metrics(
        self, step_idx: int, batch: TrainingBatch, fb_result: Any
    ) -> dict[str, float]:
        """Compute per-step metrics from the forward-backward result.

        ``fb_result`` is what the backend's
        ``forward_backward_*_async()`` future resolved to. For
        ``MockBackend`` it's a duck-typed
        :class:`~evsys_sdk.training.backend.ForwardBackwardResult`; for
        ``TinkerBackend`` it's the native ``tinker.ForwardBackwardOutput``.
        The StepBuilder is free to inspect ``fb_result.loss_fn_outputs``
        (one entry per Datum).
        """
        ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;step_idx&#x22;" type="&#x22;int&#x22;" value="null" />

    <PyParameter name="&#x22;batch&#x22;" type="&#x22;TrainingBatch&#x22;" value="null" />

    <PyParameter name="&#x22;fb_result&#x22;" type="&#x22;Any&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;dict[str, float]&#x22;" />
</PyFunction>
