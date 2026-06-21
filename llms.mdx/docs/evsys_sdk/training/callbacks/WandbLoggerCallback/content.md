# WandbLoggerCallback (/docs/evsys_sdk/training/callbacks/WandbLoggerCallback)



Log metrics + benchmark predictions to Weights & Biases.

One W\&B run per arm: opened in `on_run_start` (so it exists before the
first step) and closed in `on_run_end`. wandb is imported lazily — if it
isn't installed the callback warns once and every hook no-ops (training is
never affected; :func:`dispatch` also isolates errors). The run URL is
surfaced on `ctx.extras['wandb_url']` so a downstream logger
(evsys\_logger) can record it on the dashboard run.

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;name&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;WandbLoggerConfig&#x22;" />

<PyAttribute name="&#x22;project&#x22;" type="null" value="&#x22;project&#x22;" />

<PyAttribute name="&#x22;entity&#x22;" type="null" value="&#x22;entity&#x22;" />

<PyAttribute name="&#x22;mode&#x22;" type="null" value="&#x22;mode&#x22;" />

<PyAttribute name="&#x22;log_every&#x22;" type="null" value="&#x22;max(1, int(log_every))&#x22;" />

<PyAttribute name="&#x22;max_pred_rows&#x22;" type="null" value="&#x22;max(0, int(max_pred_rows))&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, project=None, entity=None, name=None, mode='online', log_every=1, max_pred_rows=100) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(
        self,
        *,
        project: str | None = None,
        entity: str | None = None,
        name: str | None = None,
        mode: str = "online",
        log_every: int = 1,
        max_pred_rows: int = 100,
    ) -> None:
        self.project = project
        self.entity = entity
        self.name = name
        self.mode = mode
        self.log_every = max(1, int(log_every))
        self.max_pred_rows = max(0, int(max_pred_rows))
        self._wandb: Any = None
        self._run: Any = None
        self._disabled = False
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;project&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;entity&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;mode&#x22;" type="&#x22;str&#x22;" value="&#x22;'online'&#x22;" />

    <PyParameter name="&#x22;log_every&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />

    <PyParameter name="&#x22;max_pred_rows&#x22;" type="&#x22;int&#x22;" value="&#x22;100&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_lazy_wandb&#x22;" type="&#x22;(self) -> Any&#x22;">
  <PySourceCode>
    ```python
    def _lazy_wandb(self) -> Any:
        if self._wandb is None and not self._disabled:
            try:
                import wandb  # noqa: PLC0415
                self._wandb = wandb
            except Exception:
                self._disabled = True
                logger.warning("wandb_logger: wandb not installed; disabling W&B logging")
        return self._wandb
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_run_start&#x22;" type="&#x22;(self, ctx) -> None&#x22;">
  <PySourceCode>
    ```python
    def on_run_start(self, ctx: LogContext) -> None:
        wb = self._lazy_wandb()
        if wb is None:
            return
        cfg = ctx.run_config.model_dump() if ctx.run_config is not None else {}
        project = self.project or (getattr(ctx.config, "name", None) if ctx.config else None) or "evsys"
        run_name = self.name or (getattr(ctx.run_config, "name", None) if ctx.run_config else None) or ctx.run_key
        try:
            self._run = wb.init(
                project=project, entity=self.entity, name=run_name,
                config=cfg, mode=self.mode, reinit=True,
            )
            url = getattr(self._run, "url", None)
            if url:
                ctx.extras["wandb_url"] = url
        except Exception:
            logger.exception("wandb_logger: wandb.init failed; disabling")
            self._run = None
            self._disabled = True
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
        if self._run is None or (self.log_every > 1 and (step_idx + 1) % self.log_every):
            return
        self._run.log({k: float(v) for k, v in metrics.items()}, step=step_idx)
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
        if self._run is None:
            return
        self._run.log(
            {f"val/{eval_name}/{k}": float(v) for k, v in metrics.items()}, step=step_idx
        )
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
        if self._run is None:
            return
        ename = getattr(eval_result, "name", "benchmark")
        metrics = getattr(eval_result, "metrics", {}) or {}
        payload: dict[str, Any] = {f"eval/{ename}/{k}": float(v) for k, v in metrics.items()}
        if predictions and self.max_pred_rows:
            tbl = self._wandb.Table(columns=["task_id", "expected", "reward"])
            for p in predictions[: self.max_pred_rows]:
                tbl.add_data(p.get("task_id"), str(p.get("expected")), p.get("reward"))
            payload[f"eval/{ename}/predictions"] = tbl
        self._run.log(payload, **({"step": step} if step is not None else {}))
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
        if self._run is not None:
            try:
                self._run.finish()
            finally:
                self._run = None
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
