# LoopState (/docs/evsys_sdk/training/callbacks/LoopState)



Live training-loop context handed to every callback hook.

The fields are mutated in-place by the loop as the run progresses;
`step` advances every iteration, `stop_requested` flips when a
callback calls :meth:`request_stop`. Other fields are immutable for
the run's lifetime.

## Attributes [#attributes]

<PyAttribute name="&#x22;step&#x22;" type="&#x22;int&#x22;" value="null">
  Current step index (0-based). Advances per iteration.
</PyAttribute>

<PyAttribute name="&#x22;num_steps&#x22;" type="&#x22;int&#x22;" value="null" />

<PyAttribute name="&#x22;output_dir&#x22;" type="&#x22;Path&#x22;" value="null" />

<PyAttribute name="&#x22;backend&#x22;" type="&#x22;'Backend'&#x22;" value="null" />

<PyAttribute name="&#x22;log_store&#x22;" type="&#x22;Any&#x22;" value="null">
  The same log\_store the loop writes to. Callbacks can also write
  auxiliary rows (e.g. `log_metrics(\{"debug/x": 1\}, step=...)`).
</PyAttribute>

<PyAttribute name="&#x22;checkpoint_mgr&#x22;" type="&#x22;'CheckpointManager'&#x22;" value="null" />

<PyAttribute name="&#x22;stop_requested&#x22;" type="&#x22;bool&#x22;" value="&#x22;False&#x22;" />

<PyAttribute name="&#x22;ctx&#x22;" type="&#x22;'LogContext | None'&#x22;" value="&#x22;None&#x22;">
  The experiment-wide :class:`LogContext` (shared with the Experiment-scope
  hooks). `None` for a bare `run_experiment` with no Experiment driving it;
  populated when the Experiment threads callbacks into the loop.
</PyAttribute>

## Functions [#functions]

<PyFunction name="&#x22;request_stop&#x22;" type="&#x22;(self) -> None&#x22;">
  Signal the loop to break after the current step completes.

  Used by EarlyStoppingCallback and similar policies. The loop honours
  it at the top of the next iteration so the current step's writes
  flush cleanly first.

  <PySourceCode>
    ```python
    def request_stop(self) -> None:
        """Signal the loop to break after the current step completes.

        Used by EarlyStoppingCallback and similar policies. The loop honours
        it at the top of the next iteration so the current step's writes
        flush cleanly first.
        """
        self.stop_requested = True
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, step, num_steps, output_dir, backend, log_store, checkpoint_mgr, stop_requested=False, ctx=None) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int&#x22;" value="null" />

    <PyParameter name="&#x22;num_steps&#x22;" type="&#x22;int&#x22;" value="null" />

    <PyParameter name="&#x22;output_dir&#x22;" type="&#x22;Path&#x22;" value="null" />

    <PyParameter name="&#x22;backend&#x22;" type="&#x22;'Backend'&#x22;" value="null" />

    <PyParameter name="&#x22;log_store&#x22;" type="&#x22;Any&#x22;" value="null" />

    <PyParameter name="&#x22;checkpoint_mgr&#x22;" type="&#x22;'CheckpointManager'&#x22;" value="null" />

    <PyParameter name="&#x22;stop_requested&#x22;" type="&#x22;bool&#x22;" value="&#x22;False&#x22;" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;'LogContext | None'&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
