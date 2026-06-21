# Callback (/docs/evsys_sdk/training/callbacks/Callback)



Override any of these. Defaults are no-ops so callbacks don't have to
implement every hook.

Hook signatures are positional + typed (not a generic event-bag) so
IDE autocomplete and static type checking still work. Each hook fires
at exactly one moment in the loop — see the docstrings.

All hooks are sync (`def`, not `async`). If you need to schedule
async work, spawn `asyncio.create_task(...)` from inside the hook.

## Functions [#functions]

<PyFunction name="&#x22;on_train_start&#x22;" type="&#x22;(self, state) -> None&#x22;">
  Fires once before the for-loop starts. Open log files, register
  a wandb run, snapshot the config — anything that should happen
  before the first step.

  <PySourceCode>
    ```python
    def on_train_start(self, state: LoopState) -> None:
        """Fires once before the for-loop starts. Open log files, register
        a wandb run, snapshot the config — anything that should happen
        before the first step."""
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;state&#x22;" type="&#x22;LoopState&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_train_end&#x22;" type="&#x22;(self, state, artifacts) -> None&#x22;">
  Fires once after the loop completes (including the final
  checkpoint save). Flush summary writes, close files.

  <PySourceCode>
    ```python
    def on_train_end(self, state: LoopState, artifacts: "LoopArtifacts") -> None:
        """Fires once after the loop completes (including the final
        checkpoint save). Flush summary writes, close files."""
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;state&#x22;" type="&#x22;LoopState&#x22;" value="null" />

    <PyParameter name="&#x22;artifacts&#x22;" type="&#x22;'LoopArtifacts'&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_step_end&#x22;" type="&#x22;(self, state, step_idx, batch, metrics) -> None&#x22;">
  Fires after every train step's metric row is written. The
  universal "do something per step" hook (printing, plotting,
  custom metric derivations, gradient debugging).

  <PySourceCode>
    ```python
    def on_step_end(
        self,
        state: LoopState,
        step_idx: int,
        batch: "TrainingBatch",
        metrics: dict[str, float],
    ) -> None:
        """Fires after every train step's metric row is written. The
        universal "do something per step" hook (printing, plotting,
        custom metric derivations, gradient debugging)."""
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;state&#x22;" type="&#x22;LoopState&#x22;" value="null" />

    <PyParameter name="&#x22;step_idx&#x22;" type="&#x22;int&#x22;" value="null" />

    <PyParameter name="&#x22;batch&#x22;" type="&#x22;'TrainingBatch'&#x22;" value="null" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_checkpoint&#x22;" type="&#x22;(self, state, row) -> None&#x22;">
  Fires after each checkpoint manifest row is recorded. Useful
  for shipping to S3, pruning old checkpoints, kicking a side eval.

  <PySourceCode>
    ```python
    def on_checkpoint(self, state: LoopState, row: "ManifestRow") -> None:
        """Fires after each checkpoint manifest row is recorded. Useful
        for shipping to S3, pruning old checkpoints, kicking a side eval."""
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;state&#x22;" type="&#x22;LoopState&#x22;" value="null" />

    <PyParameter name="&#x22;row&#x22;" type="&#x22;'ManifestRow'&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_eval&#x22;" type="&#x22;(self, state, step_idx, eval_name, metrics) -> None&#x22;">
  Fires per evaluator after each in-loop eval completes. Useful
  for pushing to a dashboard, plotting val curves, driving
  early-stopping decisions. (Logger callbacks that also need the
  rollout predictions should use :meth:`on_benchmark_eval`, which the
  loop fires for benchmark evaluators with the full payload.)

  <PySourceCode>
    ```python
    def on_eval(
        self,
        state: LoopState,
        step_idx: int,
        eval_name: str,
        metrics: dict[str, float],
    ) -> None:
        """Fires per evaluator after each in-loop eval completes. Useful
        for pushing to a dashboard, plotting val curves, driving
        early-stopping decisions. (Logger callbacks that also need the
        rollout predictions should use :meth:`on_benchmark_eval`, which the
        loop fires for benchmark evaluators with the full payload.)"""
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;state&#x22;" type="&#x22;LoopState&#x22;" value="null" />

    <PyParameter name="&#x22;step_idx&#x22;" type="&#x22;int&#x22;" value="null" />

    <PyParameter name="&#x22;eval_name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_experiment_start&#x22;" type="&#x22;(self, ctx) -> None&#x22;">
  Fires once at the start of an experiment, before any arm. A logger
  creates the experiment record here (`ctx.ids['experiment_id'] = ...`).

  <PySourceCode>
    ```python
    def on_experiment_start(self, ctx: LogContext) -> None:
        """Fires once at the start of an experiment, before any arm. A logger
        creates the experiment record here (``ctx.ids['experiment_id'] = ...``)."""
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;LogContext&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_group_start&#x22;" type="&#x22;(self, ctx, group_name) -> None&#x22;">
  Fires when a new run-group is needed (n\_repeats replicates or
  continual stages). A logger creates the group
  (`ctx.ids[f'group:\{group_name\}'] = ...`).

  <PySourceCode>
    ```python
    def on_group_start(self, ctx: LogContext, group_name: str) -> None:
        """Fires when a new run-group is needed (n_repeats replicates or
        continual stages). A logger creates the group
        (``ctx.ids[f'group:{group_name}'] = ...``)."""
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;LogContext&#x22;" value="null" />

    <PyParameter name="&#x22;group_name&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_run_start&#x22;" type="&#x22;(self, ctx) -> None&#x22;">
  Fires per arm, before training. `ctx.run_config` is set. A logger
  opens its run-scoped sink (wandb.init / create\_run →
  `ctx.ids['run_id']`), reading `ctx.ids['experiment_id']` /
  `ctx.ids[f'group:\{ctx.group_name\}']` to parent it.

  <PySourceCode>
    ```python
    def on_run_start(self, ctx: LogContext) -> None:
        """Fires per arm, before training. ``ctx.run_config`` is set. A logger
        opens its run-scoped sink (wandb.init / create_run →
        ``ctx.ids['run_id']``), reading ``ctx.ids['experiment_id']`` /
        ``ctx.ids[f'group:{ctx.group_name}']`` to parent it."""
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;LogContext&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_benchmark_eval&#x22;" type="&#x22;(self, ctx, eval_result, predictions, *, step=None) -> None&#x22;">
  Fires per benchmark scored — in-loop (`step` = the train step) or
  post-training (`step=None`). Carries metrics + breakdowns + tags
  (on `eval_result`) AND the per-task prediction rows. A logger
  creates one eval row per `(eval_result.name, step)` and persists the
  predictions.

  <PySourceCode>
    ```python
    def on_benchmark_eval(
        self,
        ctx: LogContext,
        eval_result: "EvalResult",
        predictions: list[dict],
        *,
        step: int | None = None,
    ) -> None:
        """Fires per benchmark scored — in-loop (``step`` = the train step) or
        post-training (``step=None``). Carries metrics + breakdowns + tags
        (on ``eval_result``) AND the per-task prediction rows. A logger
        creates one eval row per ``(eval_result.name, step)`` and persists the
        predictions."""
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;LogContext&#x22;" value="null" />

    <PyParameter name="&#x22;eval_result&#x22;" type="&#x22;'EvalResult'&#x22;" value="null" />

    <PyParameter name="&#x22;predictions&#x22;" type="&#x22;list[dict]&#x22;" value="null" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_run_end&#x22;" type="&#x22;(self, ctx, run_result, arm) -> None&#x22;">
  Fires per arm, after eval, before the run is marked completed. A
  logger flushes/closes its run-scoped sink (wandb.finish) and records
  the final status (update\_run).

  <PySourceCode>
    ```python
    def on_run_end(
        self, ctx: LogContext, run_result: "RunResult", arm: "ArmResult",
    ) -> None:
        """Fires per arm, after eval, before the run is marked completed. A
        logger flushes/closes its run-scoped sink (wandb.finish) and records
        the final status (update_run)."""
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;LogContext&#x22;" value="null" />

    <PyParameter name="&#x22;run_result&#x22;" type="&#x22;'RunResult'&#x22;" value="null" />

    <PyParameter name="&#x22;arm&#x22;" type="&#x22;'ArmResult'&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_experiment_end&#x22;" type="&#x22;(self, ctx, result) -> None&#x22;">
  Fires once at the end of the experiment. Final summary / flush.

  <PySourceCode>
    ```python
    def on_experiment_end(
        self, ctx: LogContext, result: "ExperimentResult",
    ) -> None:
        """Fires once at the end of the experiment. Final summary / flush."""
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;LogContext&#x22;" value="null" />

    <PyParameter name="&#x22;result&#x22;" type="&#x22;'ExperimentResult'&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
