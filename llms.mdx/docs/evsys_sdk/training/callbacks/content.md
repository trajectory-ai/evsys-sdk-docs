# callbacks (/docs/evsys_sdk/training/callbacks)



Callbacks — hook points inside :class:`~evsys_sdk.training.loop.TrainingLoop`
for logging, debugging, visualization, and early stopping.

The :class:`Callback` base has no-op defaults for every hook. Override only
the ones you need:

class MyDebug(Callback):
def on\_step\_end(self, state, step\_idx, batch, metrics):
if step\_idx % 100 == 0:
print(f"\[\{step\_idx}] \{metrics}")

Then hand to the loop:

loop = TrainingLoop(..., callbacks=\[MyDebug(), CsvMetricsCallback(...)])

A failing callback never kills the loop — the exception is logged at
WARNING and the loop continues. Same pattern as failing evaluators.

For early stopping (e.g. on a plateau in val pass\_rate), call
`state.request_stop()` from inside a hook and the loop breaks after the
current step. Researchers writing a new "stop after N evals without
improvement" policy subclass :class:`EarlyStoppingCallback`.

Three built-ins ship today:

* :class:`PrintProgressCallback` — tqdm-style stdout one-liner per step.
* :class:`CsvMetricsCallback` — mirror metrics.jsonl to a per-step CSV for
  pandas-friendly inspection.
* :class:`EarlyStoppingCallback` — request\_stop after N evals with no
  improvement on a named metric.

<PyAttribute name="&#x22;logger&#x22;" type="null" value="&#x22;logging.getLogger(__name__)&#x22;" />

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['Callback', 'CsvMetricsCallback', 'EarlyStoppingCallback', 'DebugLoggerCallback', 'EvsysLoggerCallback', 'LocalLoggerCallback', 'LogContext', 'LoopState', 'PrintProgressCallback', 'TensorBoardLoggerCallback', 'WandbLoggerCallback', 'build_callbacks', 'dispatch']&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;LogContext&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/LogContext&#x22;" />

      <Card title="&#x22;LoopState&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/LoopState&#x22;" />

      <Card title="&#x22;Callback&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/Callback&#x22;" />

      <Card title="&#x22;PrintProgressConfig&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/PrintProgressConfig&#x22;" />

      <Card title="&#x22;PrintProgressCallback&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/PrintProgressCallback&#x22;" />

      <Card title="&#x22;CsvMetricsConfig&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/CsvMetricsConfig&#x22;" />

      <Card title="&#x22;CsvMetricsCallback&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/CsvMetricsCallback&#x22;" />

      <Card title="&#x22;EarlyStoppingConfig&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/EarlyStoppingConfig&#x22;" />

      <Card title="&#x22;EarlyStoppingCallback&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/EarlyStoppingCallback&#x22;" />

      <Card title="&#x22;WandbLoggerConfig&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/WandbLoggerConfig&#x22;" />

      <Card title="&#x22;WandbLoggerCallback&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/WandbLoggerCallback&#x22;" />

      <Card title="&#x22;TensorBoardLoggerConfig&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/TensorBoardLoggerConfig&#x22;" />

      <Card title="&#x22;TensorBoardLoggerCallback&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/TensorBoardLoggerCallback&#x22;" />

      <Card title="&#x22;LocalLoggerConfig&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/LocalLoggerConfig&#x22;" />

      <Card title="&#x22;LocalLoggerCallback&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/LocalLoggerCallback&#x22;" />

      <Card title="&#x22;EvsysLoggerConfig&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/EvsysLoggerConfig&#x22;" />

      <Card title="&#x22;EvsysLoggerCallback&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/EvsysLoggerCallback&#x22;" />

      <Card title="&#x22;DebugLoggerConfig&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/DebugLoggerConfig&#x22;" />

      <Card title="&#x22;DebugLoggerCallback&#x22;" href="&#x22;/docs/evsys_sdk/training/callbacks/DebugLoggerCallback&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;dispatch&#x22;" type="&#x22;(callbacks, hook, *args, **kwargs) -> None&#x22;">
      Call `hook` on every callback. A raising callback NEVER propagates —
      the exception is logged at WARNING and the next callback runs. Used by both
      the TrainingLoop (loop-scope hooks) and the Experiment (experiment-scope
      hooks) so error isolation is identical everywhere.

      <PySourceCode>
        ```python
        def dispatch(callbacks: list[Callback], hook: str, *args: Any, **kwargs: Any) -> None:
            """Call ``hook`` on every callback. A raising callback NEVER propagates —
            the exception is logged at WARNING and the next callback runs. Used by both
            the TrainingLoop (loop-scope hooks) and the Experiment (experiment-scope
            hooks) so error isolation is identical everywhere."""
            for cb in callbacks or []:
                fn = getattr(cb, hook, None)
                if fn is None:
                    continue
                try:
                    fn(*args, **kwargs)
                except Exception:
                    logger.exception(
                        "callback %s.%s raised; continuing", type(cb).__name__, hook,
                    )
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;callbacks&#x22;" type="&#x22;list[Callback]&#x22;" value="null" />

        <PyParameter name="&#x22;hook&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;args&#x22;" type="&#x22;Any&#x22;" value="&#x22;()&#x22;" />

        <PyParameter name="&#x22;kwargs&#x22;" type="&#x22;Any&#x22;" value="&#x22;{}&#x22;" />
      </div>

      <PyFunctionReturn type="&#x22;None&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;build_callbacks&#x22;" type="&#x22;(specs) -> list[Callback]&#x22;">
      Materialize callbacks from a list of `\{kind, params\}` specs.

      Each `spec` may be a :class:`~evsys_sdk.config.CallbackSpec` (or any
      object/dict with `kind` + `params`). The `kind` is resolved through
      the callback registry; `params` are validated against the callback's
      `Config` (so a YAML typo fails loudly) before construction. Users
      register their own callbacks with `@register_callback("my_name")` — see
      the built-ins above for the contract (`name` + `Config` ClassVars).

      <PySourceCode>
        ```python
        def build_callbacks(specs: Any) -> list[Callback]:
            """Materialize callbacks from a list of ``{kind, params}`` specs.

            Each ``spec`` may be a :class:`~evsys_sdk.config.CallbackSpec` (or any
            object/dict with ``kind`` + ``params``). The ``kind`` is resolved through
            the callback registry; ``params`` are validated against the callback's
            ``Config`` (so a YAML typo fails loudly) before construction. Users
            register their own callbacks with ``@register_callback("my_name")`` — see
            the built-ins above for the contract (``name`` + ``Config`` ClassVars).
            """
            out: list[Callback] = []
            for spec in specs or []:
                kind = spec.kind if hasattr(spec, "kind") else spec["kind"]
                raw = (spec.params if hasattr(spec, "params") else spec.get("params")) or {}
                cls = get_callback(kind)
                cfg = getattr(cls, "Config", None)
                params = cfg(**raw).model_dump() if cfg is not None else dict(raw)
                out.append(cls(**params))
            return out
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;specs&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;list[evsys_sdk.training.callbacks.Callback]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_fmt_value&#x22;" type="&#x22;(v) -> str&#x22;">
      <PySourceCode>
        ```python
        def _fmt_value(v: Any) -> str:
            try:
                f = float(v)
                if abs(f) < 1e-3 or abs(f) >= 1e6:
                    return f"{f:.2e}"
                return f"{f:.4f}"
            except Exception:
                return str(v)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;v&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;str&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
