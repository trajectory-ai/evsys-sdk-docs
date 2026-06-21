# EvsysLoggerCallback (/docs/evsys_sdk/training/callbacks/EvsysLoggerCallback)



Persist EVERYTHING to the evsys dashboard store via callbacks — the
experiment/group/run records, per-step metrics, checkpoints, and benchmark
evals + predictions. Keyed off the shared `ctx.ids` it populates
(`experiment_id` → `group:\<name>` → `run_id`).

This is the "callbacks own the store" mode: construct `Experiment` WITHOUT
a `store=` (so the orchestrator makes no store calls) and add this
callback instead. If `Experiment` already has a store (`ctx.store` set),
this callback disables itself to avoid double-writing. The store handle is
built lazily from the environment (EvsysStore) unless one is injected
(`cb._store = ...`) — the test seam.

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'evsys_logger'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;EvsysLoggerConfig&#x22;" />

<PyAttribute name="&#x22;project_id&#x22;" type="null" value="&#x22;project_id&#x22;" />

<PyAttribute name="&#x22;flush_every&#x22;" type="null" value="&#x22;max(1, int(flush_every))&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, project_id=None, flush_every=1) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, *, project_id: str | None = None, flush_every: int = 1) -> None:
        self.project_id = project_id
        self.flush_every = max(1, int(flush_every))
        self._store: Any = None
        self._disabled = False
        self._buf: list[tuple[int, dict, str]] = []
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;project_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;flush_every&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_ensure_store&#x22;" type="&#x22;(self, ctx) -> Any&#x22;">
  <PySourceCode>
    ```python
    def _ensure_store(self, ctx: LogContext) -> Any:
        if self._disabled:
            return None
        if ctx.store is not None:
            self._disabled = True
            logger.warning(
                "evsys_logger: Experiment already has a store; disabling to avoid "
                "double-writes (drop store= from Experiment to use this callback)"
            )
            return None
        if self._store is None:
            try:
                from ..store import EvsysStore  # noqa: PLC0415
                self._store = EvsysStore(project_id=self.project_id)
            except Exception:
                self._disabled = True
                logger.warning("evsys_logger: no usable store (missing EVSYS_API_KEY?); disabling")
        return self._store
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;LogContext&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_id&#x22;" type="&#x22;(resp) -> str | None&#x22;">
  <PySourceCode>
    ```python
    @staticmethod
    def _id(resp: Any) -> str | None:
        return resp.get("id") if isinstance(resp, dict) else None
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;resp&#x22;" type="&#x22;Any&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str | None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_experiment_start&#x22;" type="&#x22;(self, ctx) -> None&#x22;">
  <PySourceCode>
    ```python
    def on_experiment_start(self, ctx: LogContext) -> None:
        store = self._ensure_store(ctx)
        if store is None:
            return
        meta = (getattr(ctx.config, "metadata", None) or {}) if ctx.config else {}
        resp = store.create_experiment(
            experiment_name=getattr(ctx.config, "name", "experiment"),
            hypothesis=meta.get("hypothesis"),
            tags=list(meta.get("tags") or []) or None,
        )
        eid = self._id(resp)
        if eid:
            ctx.ids["experiment_id"] = eid
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;LogContext&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_group_start&#x22;" type="&#x22;(self, ctx, group_name) -> None&#x22;">
  <PySourceCode>
    ```python
    def on_group_start(self, ctx: LogContext, group_name: str) -> None:
        if self._disabled or self._store is None:
            return
        resp = self._store.create_group(ctx.ids.get("experiment_id"), group_name)
        gid = self._id(resp)
        if gid:
            ctx.ids[f"group:{group_name}"] = gid
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
  <PySourceCode>
    ```python
    def on_run_start(self, ctx: LogContext) -> None:
        if self._disabled or self._store is None:
            return
        rc = ctx.run_config
        gid = ctx.ids.get(f"group:{ctx.group_name}") if ctx.group_name else None
        resp = self._store.create_run(
            experiment_id=ctx.ids.get("experiment_id"),
            group_id=gid,
            recipe_kind=getattr(getattr(rc, "algorithm", None), "kind", None),
            run_config=rc.model_dump() if rc is not None else None,
            seed=getattr(rc, "seed", None),
            status="running",
            wandb_run_url=ctx.extras.get("wandb_url"),
        )
        rid = self._id(resp)
        if rid:
            ctx.ids["run_id"] = rid
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;LogContext&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_benchmark_eval&#x22;" type="&#x22;(self, ctx, eval_result, predictions, *, step=None) -> None&#x22;">
  <PySourceCode>
    ```python
    def on_benchmark_eval(self, ctx, eval_result, predictions, *, step=None) -> None:
        if self._disabled or self._store is None:
            return
        run_id = ctx.ids.get("run_id")
        if not run_id:
            return
        resp = self._store.create_eval(
            run_id=run_id,
            benchmark_id=getattr(eval_result, "benchmark_id", None),
            metrics=dict(getattr(eval_result, "metrics", {}) or {}),
            breakdowns=dict(getattr(eval_result, "breakdowns", {}) or {}) or None,
            step=step,
        )
        eval_id = self._id(resp)
        if predictions:
            from .harbor_eval import upload_eval_rollouts  # noqa: PLC0415
            rows = [{**p, "eval_id": eval_id} for p in predictions]
            upload_eval_rollouts(self._store, run_id, rows)
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
        if self._disabled or self._store is None:
            return
        self._flush(ctx)
        run_id = ctx.ids.get("run_id")
        if run_id:
            status = getattr(run_result, "status", None) or getattr(arm, "status", None)
            patch = {"status": status}
            err = getattr(run_result, "error", None) or getattr(arm, "error", None)
            if err:
                patch["error_message"] = err
            self._store.update_run(run_id, **patch)
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

<PyFunction name="&#x22;on_step_end&#x22;" type="&#x22;(self, state, step_idx, batch, metrics) -> None&#x22;">
  <PySourceCode>
    ```python
    def on_step_end(self, state: LoopState, step_idx, batch, metrics) -> None:
        if self._disabled or self._store is None:
            return
        self._buf.append((step_idx, {k: float(v) for k, v in metrics.items()}, "train"))
        if len(self._buf) >= self.flush_every:
            self._flush(state.ctx)
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
        if self._disabled or self._store is None:
            return
        self._buf.append(
            (step_idx, {f"{eval_name}/{k}": float(v) for k, v in metrics.items()}, "val")
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

<PyFunction name="&#x22;on_checkpoint&#x22;" type="&#x22;(self, state, row) -> None&#x22;">
  <PySourceCode>
    ```python
    def on_checkpoint(self, state: LoopState, row) -> None:
        if self._disabled or self._store is None:
            return
        ctx = state.ctx
        run_id = ctx.ids.get("run_id") if ctx else None
        uri = getattr(row, "sampler_path", None) or getattr(row, "state_path", None)
        if run_id and uri:
            self._store.add_checkpoint(
                run_id, uri=uri, label=getattr(row, "name", None), step=getattr(row, "batch", None),
            )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;state&#x22;" type="&#x22;LoopState&#x22;" value="null" />

    <PyParameter name="&#x22;row&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_flush&#x22;" type="&#x22;(self, ctx) -> None&#x22;">
  <PySourceCode>
    ```python
    def _flush(self, ctx: LogContext | None) -> None:
        if self._store is None or not self._buf or ctx is None:
            return
        run_id = ctx.ids.get("run_id")
        if not run_id:
            self._buf.clear()
            return
        for step, metrics, split in self._buf:
            try:
                self._store.log_metrics(run_id=run_id, step=int(step), metrics=metrics, split=split)
            except Exception:
                logger.warning("evsys_logger: log_metrics failed at step %s", step, exc_info=True)
        self._buf.clear()
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;LogContext | None&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
