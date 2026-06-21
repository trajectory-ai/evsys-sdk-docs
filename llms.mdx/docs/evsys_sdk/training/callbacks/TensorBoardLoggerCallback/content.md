# TensorBoardLoggerCallback (/docs/evsys_sdk/training/callbacks/TensorBoardLoggerCallback)



Log scalar metrics + eval scalars to TensorBoard, one event dir per arm.

Opens a `SummaryWriter` in `on_run_start` (lazy import so the callback
registers without torch installed) and closes it in `on_run_end`.

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'tensorboard_logger'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;TensorBoardLoggerConfig&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, log_dir=None, flush_secs=30) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, *, log_dir: str | None = None, flush_secs: int = 30) -> None:
        self._log_dir = log_dir
        self._flush_secs = int(flush_secs)
        self._writer: Any = None
        self._disabled = False
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;log_dir&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;flush_secs&#x22;" type="&#x22;int&#x22;" value="&#x22;30&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_run_start&#x22;" type="&#x22;(self, ctx) -> None&#x22;">
  <PySourceCode>
    ```python
    def on_run_start(self, ctx: LogContext) -> None:
        if self._disabled:
            return
        try:
            from torch.utils.tensorboard import SummaryWriter  # noqa: PLC0415
        except Exception:
            self._disabled = True
            logger.warning("tensorboard_logger: tensorboard/torch missing; disabling")
            return
        path = self._log_dir or str(Path(ctx.output_dir) / "tb" / (ctx.run_key or "run"))
        self._writer = SummaryWriter(log_dir=path, flush_secs=self._flush_secs)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;LogContext&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_step_end&#x22;" type="&#x22;(self, state, step_idx, batch, metrics) -> None&#x22;">
  <PySourceCode>
    ```python
    def on_step_end(self, state: LoopState, step_idx, batch, metrics) -> None:
        if self._writer is None:
            return
        for k, v in metrics.items():
            self._writer.add_scalar(k, float(v), global_step=step_idx)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;state&#x22;" type="&#x22;LoopState&#x22;" value="null" />

    <PyParameter name="&#x22;step_idx&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;batch&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;metrics&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_eval&#x22;" type="&#x22;(self, state, step_idx, eval_name, metrics) -> None&#x22;">
  <PySourceCode>
    ```python
    def on_eval(self, state: LoopState, step_idx, eval_name, metrics) -> None:
        if self._writer is None:
            return
        for k, v in metrics.items():
            self._writer.add_scalar(f"val/{eval_name}/{k}", float(v), global_step=step_idx)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;state&#x22;" type="&#x22;LoopState&#x22;" value="null" />

    <PyParameter name="&#x22;step_idx&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;eval_name&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;metrics&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_benchmark_eval&#x22;" type="&#x22;(self, ctx, eval_result, predictions, *, step=None) -> None&#x22;">
  <PySourceCode>
    ```python
    def on_benchmark_eval(self, ctx, eval_result, predictions, *, step=None) -> None:
        if self._writer is None:
            return
        ename = getattr(eval_result, "name", "benchmark")
        gs = step if step is not None else 0
        for k, v in (getattr(eval_result, "metrics", {}) or {}).items():
            self._writer.add_scalar(f"eval/{ename}/{k}", float(v), global_step=gs)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;eval_result&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;predictions&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;step&#x22;" type="null" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_run_end&#x22;" type="&#x22;(self, ctx, run_result, arm) -> None&#x22;">
  <PySourceCode>
    ```python
    def on_run_end(self, ctx, run_result, arm) -> None:
        if self._writer is not None:
            try:
                self._writer.close()
            finally:
                self._writer = None
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_result&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;arm&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
