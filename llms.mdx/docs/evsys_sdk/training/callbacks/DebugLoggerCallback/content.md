# DebugLoggerCallback (/docs/evsys_sdk/training/callbacks/DebugLoggerCallback)



Pretty-print EVERYTHING handed to each callback hook — every lifecycle
and loop event, with its arguments summarized. Pure introspection (no
persistence); drop it into `callbacks:` to see exactly what the logger
callbacks receive and in what order.

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'debug_logger'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;DebugLoggerConfig&#x22;" />

<PyAttribute name="&#x22;max_len&#x22;" type="null" value="&#x22;int(max_len)&#x22;" />

<PyAttribute name="&#x22;max_pred_rows&#x22;" type="null" value="&#x22;int(max_pred_rows)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, max_len=400, max_pred_rows=3) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, *, max_len: int = 400, max_pred_rows: int = 3) -> None:
        self.max_len = int(max_len)
        self.max_pred_rows = int(max_pred_rows)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;max_len&#x22;" type="&#x22;int&#x22;" value="&#x22;400&#x22;" />

    <PyParameter name="&#x22;max_pred_rows&#x22;" type="&#x22;int&#x22;" value="&#x22;3&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_short&#x22;" type="&#x22;(self, v) -> str&#x22;">
  <PySourceCode>
    ```python
    def _short(self, v: Any) -> str:
        s = repr(v)
        return s if len(s) <= self.max_len else s[: self.max_len] + f"… (+{len(s) - self.max_len} chars)"
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;v&#x22;" type="&#x22;Any&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_ctx&#x22;" type="&#x22;(self, ctx) -> dict&#x22;">
  <PySourceCode>
    ```python
    def _ctx(self, ctx: Any) -> dict:
        rc = getattr(ctx, "run_config", None)
        return {
            "run_key": getattr(ctx, "run_key", None),
            "group_name": getattr(ctx, "group_name", None),
            "ids": dict(getattr(ctx, "ids", {}) or {}),
            "store": type(getattr(ctx, "store", None)).__name__ if getattr(ctx, "store", None) else None,
            "extras": dict(getattr(ctx, "extras", {}) or {}),
            "run_config.name": getattr(rc, "name", None),
            "output_dir": str(getattr(ctx, "output_dir", "")),
        }
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;Any&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_state&#x22;" type="&#x22;(self, state) -> dict&#x22;">
  <PySourceCode>
    ```python
    def _state(self, state: Any) -> dict:
        return {
            "step": getattr(state, "step", None),
            "num_steps": getattr(state, "num_steps", None),
            "has_ctx": getattr(state, "ctx", None) is not None,
        }
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;state&#x22;" type="&#x22;Any&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_emit&#x22;" type="&#x22;(self, hook, fields) -> None&#x22;">
  <PySourceCode>
    ```python
    def _emit(self, hook: str, fields: dict) -> None:
        print(f"\n🔍 [debug_logger] {hook}", flush=True)
        for k, v in fields.items():
            print(f"      {k} = {self._short(v)}", flush=True)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;hook&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;fields&#x22;" type="&#x22;dict&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_experiment_start&#x22;" type="&#x22;(self, ctx)&#x22;">
  <PySourceCode>
    ```python
    def on_experiment_start(self, ctx):
        self._emit("on_experiment_start", {"ctx": self._ctx(ctx)})
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="null" />
</PyFunction>

<PyFunction name="&#x22;on_group_start&#x22;" type="&#x22;(self, ctx, group_name)&#x22;">
  <PySourceCode>
    ```python
    def on_group_start(self, ctx, group_name):
        self._emit("on_group_start", {"group_name": group_name, "ctx": self._ctx(ctx)})
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;group_name&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="null" />
</PyFunction>

<PyFunction name="&#x22;on_run_start&#x22;" type="&#x22;(self, ctx)&#x22;">
  <PySourceCode>
    ```python
    def on_run_start(self, ctx):
        self._emit("on_run_start", {"ctx": self._ctx(ctx)})
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="null" />
</PyFunction>

<PyFunction name="&#x22;on_benchmark_eval&#x22;" type="&#x22;(self, ctx, eval_result, predictions, *, step=None)&#x22;">
  <PySourceCode>
    ```python
    def on_benchmark_eval(self, ctx, eval_result, predictions, *, step=None):
        self._emit("on_benchmark_eval", {
            "step": step,
            "eval_result.name": getattr(eval_result, "name", None),
            "eval_result.metrics": getattr(eval_result, "metrics", None),
            "eval_result.breakdowns": getattr(eval_result, "breakdowns", None),
            "eval_result.tags": getattr(eval_result, "tags", None),
            "n_predictions": len(predictions),
            "predictions[:n]": predictions[: self.max_pred_rows],
            "ctx.ids": dict(getattr(ctx, "ids", {}) or {}),
        })
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;eval_result&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;predictions&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;step&#x22;" type="null" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="null" />
</PyFunction>

<PyFunction name="&#x22;on_run_end&#x22;" type="&#x22;(self, ctx, run_result, arm)&#x22;">
  <PySourceCode>
    ```python
    def on_run_end(self, ctx, run_result, arm):
        self._emit("on_run_end", {
            "run_result.status": getattr(run_result, "status", None),
            "run_result.metrics": getattr(run_result, "metrics", None),
            "arm.name": getattr(arm, "name", None),
            "arm.status": getattr(arm, "status", None),
            "ctx.ids": dict(getattr(ctx, "ids", {}) or {}),
        })
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_result&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;arm&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="null" />
</PyFunction>

<PyFunction name="&#x22;on_experiment_end&#x22;" type="&#x22;(self, ctx, result)&#x22;">
  <PySourceCode>
    ```python
    def on_experiment_end(self, ctx, result):
        self._emit("on_experiment_end", {
            "result.status": getattr(result, "status", None),
            "result.best_arm": getattr(getattr(result, "best_arm", None), "name", None),
            "result.best_score": getattr(result, "best_score", None),
            "result.conclusion": getattr(result, "conclusion", None),
        })
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;result&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="null" />
</PyFunction>

<PyFunction name="&#x22;on_train_start&#x22;" type="&#x22;(self, state)&#x22;">
  <PySourceCode>
    ```python
    def on_train_start(self, state):
        self._emit("on_train_start", {"state": self._state(state)})
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;state&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="null" />
</PyFunction>

<PyFunction name="&#x22;on_step_end&#x22;" type="&#x22;(self, state, step_idx, batch, metrics)&#x22;">
  <PySourceCode>
    ```python
    def on_step_end(self, state, step_idx, batch, metrics):
        self._emit("on_step_end", {
            "step_idx": step_idx,
            "metrics": metrics,
            "batch.loss_fn": getattr(batch, "loss_fn", None),
            "batch.n_data": len(getattr(batch, "data", []) or []),
            "batch.metrics": getattr(batch, "metrics", None),
            "state": self._state(state),
        })
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;state&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;step_idx&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;batch&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;metrics&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="null" />
</PyFunction>

<PyFunction name="&#x22;on_eval&#x22;" type="&#x22;(self, state, step_idx, eval_name, metrics)&#x22;">
  <PySourceCode>
    ```python
    def on_eval(self, state, step_idx, eval_name, metrics):
        self._emit("on_eval", {"step_idx": step_idx, "eval_name": eval_name, "metrics": metrics})
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;state&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;step_idx&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;eval_name&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;metrics&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="null" />
</PyFunction>

<PyFunction name="&#x22;on_checkpoint&#x22;" type="&#x22;(self, state, row)&#x22;">
  <PySourceCode>
    ```python
    def on_checkpoint(self, state, row):
        self._emit("on_checkpoint", {
            "row.name": getattr(row, "name", None),
            "row.batch": getattr(row, "batch", None),
            "row.sampler_path": getattr(row, "sampler_path", None),
            "row.state_path": getattr(row, "state_path", None),
        })
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;state&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;row&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="null" />
</PyFunction>

<PyFunction name="&#x22;on_train_end&#x22;" type="&#x22;(self, state, artifacts)&#x22;">
  <PySourceCode>
    ```python
    def on_train_end(self, state, artifacts):
        self._emit("on_train_end", {
            "artifacts.total_requested_steps": getattr(artifacts, "total_requested_steps", None),
            "artifacts.train_seconds": getattr(artifacts, "train_seconds", None),
            "artifacts.run_dir": str(getattr(artifacts, "run_dir", "")),
            "n_checkpoints": len(getattr(artifacts, "checkpoints", []) or []),
        })
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;state&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;artifacts&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="null" />
</PyFunction>
