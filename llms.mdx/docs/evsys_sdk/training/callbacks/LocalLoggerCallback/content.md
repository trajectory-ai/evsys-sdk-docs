# LocalLoggerCallback (/docs/evsys_sdk/training/callbacks/LocalLoggerCallback)



Human-readable local logging: prints what's happening per step AND
persists metrics + benchmark predictions to files under the run dir.

Writes `\<output_dir>/\<run_key>/` : `metrics.jsonl` (train + val rows),
`predictions/\<name>.jsonl` (per benchmark), and `summary.md` at
run end. The "print what's happening" requirement is the per-step one-liner
(cadence `print_every`).

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'local_logger'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;LocalLoggerConfig&#x22;" />

<PyAttribute name="&#x22;print_every&#x22;" type="null" value="&#x22;int(print_every)&#x22;" />

<PyAttribute name="&#x22;keys&#x22;" type="null" value="&#x22;keys&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, print_every=1, keys=None) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, *, print_every: int = 1, keys: list[str] | None = None) -> None:
        self.print_every = int(print_every)
        self.keys = keys
        self._dir: Path | None = None
        self._metrics_fp: Any = None
        self._evals: list[dict] = []
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;print_every&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />

    <PyParameter name="&#x22;keys&#x22;" type="&#x22;list[str] | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_run_start&#x22;" type="&#x22;(self, ctx) -> None&#x22;">
  <PySourceCode>
    ```python
    def on_run_start(self, ctx: LogContext) -> None:
        self._dir = Path(ctx.output_dir) / (ctx.run_key or "run")
        self._dir.mkdir(parents=True, exist_ok=True)
        self._metrics_fp = (self._dir / "metrics.jsonl").open("a")
        self._evals = []
        if self.print_every:
            print(f"[local_logger] run {ctx.run_key} → {self._dir}", flush=True)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;LogContext&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_write_metrics&#x22;" type="&#x22;(self, step, metrics, split) -> None&#x22;">
  <PySourceCode>
    ```python
    def _write_metrics(self, step: int, metrics: dict, split: str) -> None:
        if self._metrics_fp is None:
            return
        import json  # noqa: PLC0415
        self._metrics_fp.write(
            json.dumps({"step": step, "split": split,
                        "metrics": {k: float(v) for k, v in metrics.items()}}) + "\n"
        )
        self._metrics_fp.flush()
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int&#x22;" value="null" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;dict&#x22;" value="null" />

    <PyParameter name="&#x22;split&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;on_step_end&#x22;" type="&#x22;(self, state, step_idx, batch, metrics) -> None&#x22;">
  <PySourceCode>
    ```python
    def on_step_end(self, state: LoopState, step_idx, batch, metrics) -> None:
        self._write_metrics(step_idx, metrics, "train")
        if self.print_every and (step_idx + 1) % self.print_every == 0:
            view = metrics if self.keys is None else {k: metrics[k] for k in self.keys if k in metrics}
            line = f"[{step_idx + 1}/{state.num_steps}] " + " ".join(
                f"{k}={_fmt_value(v)}" for k, v in view.items()
            )
            print(line, flush=True)
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
        self._write_metrics(step_idx, metrics, "val")
        if self.print_every:
            cells = " ".join(f"{k}={_fmt_value(v)}" for k, v in metrics.items())
            print(f"  [eval {eval_name} @ {step_idx}] {cells}", flush=True)
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
        if self._dir is None:
            return
        import json  # noqa: PLC0415
        ename = getattr(eval_result, "name", "benchmark")
        metrics = dict(getattr(eval_result, "metrics", {}) or {})
        self._evals.append({"name": ename, "step": step, "metrics": metrics})
        if predictions:
            pdir = self._dir / "predictions"
            pdir.mkdir(exist_ok=True)
            safe = ename.replace("/", "_").replace(" ", "_")
            with (pdir / f"{safe}.jsonl").open("w") as f:
                for p in predictions:
                    f.write(json.dumps(p, default=str) + "\n")
        if self.print_every:
            cells = " ".join(f"{k}={_fmt_value(v)}" for k, v in metrics.items())
            print(f"  [benchmark {ename}] {cells}  n_pred={len(predictions)}", flush=True)
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
        if self._dir is not None:
            lines = [f"# {ctx.run_key}", ""]
            status = getattr(run_result, "status", None)
            lines.append(f"- status: {status}")
            for ev in self._evals:
                cells = ", ".join(f"{k}={v:.4f}" for k, v in ev["metrics"].items()
                                  if isinstance(v, (int, float)))
                lines.append(f"- eval **{ev['name']}** (step={ev['step']}): {cells}")
            (self._dir / "summary.md").write_text("\n".join(lines) + "\n")
        if self._metrics_fp is not None:
            try:
                self._metrics_fp.close()
            finally:
                self._metrics_fp = None
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
