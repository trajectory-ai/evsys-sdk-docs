# ArmResult (/docs/evsys_sdk/experiment/ArmResult)



One arm of the experiment (one expanded `RunConfig`).

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

<PyAttribute name="&#x22;run_config&#x22;" type="&#x22;RunConfig&#x22;" value="null" />

<PyAttribute name="&#x22;status&#x22;" type="&#x22;str&#x22;" value="null" />

<PyAttribute name="&#x22;metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="&#x22;field(default_factory=dict)&#x22;">
  Training-side metrics from `RunResult.metrics`.
</PyAttribute>

<PyAttribute name="&#x22;eval_metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="&#x22;field(default_factory=dict)&#x22;">
  Back-compat alias — mirrors `evals[\<primary>].metrics`. `primary`
  is the first `test`-tagged post-training eval, or the first
  post-training eval, or the first eval overall.
</PyAttribute>

<PyAttribute name="&#x22;eval_breakdowns&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;field(default_factory=dict)&#x22;" />

<PyAttribute name="&#x22;evals&#x22;" type="&#x22;list[EvalResult]&#x22;" value="&#x22;field(default_factory=list)&#x22;">
  All benchmarks scored against this arm — see `EvalResult`.
</PyAttribute>

<PyAttribute name="&#x22;run_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;error&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;train_seconds&#x22;" type="&#x22;float | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;eval_seconds&#x22;" type="&#x22;float | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;run_result&#x22;" type="&#x22;RunResult | None&#x22;" value="&#x22;None&#x22;">
  Raw underlying `RunResult` for advanced consumers.
</PyAttribute>

<PyAttribute name="&#x22;group_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  Dashboard `group_id` when `n_repeats > 1` (else None).
</PyAttribute>

<PyAttribute name="&#x22;group_name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  Primary's name (= the group's name) when grouped; else None.
</PyAttribute>

## Functions [#functions]

<PyFunction name="&#x22;eval&#x22;" type="&#x22;(self, name, *, step=None) -> EvalResult | None&#x22;">
  Look up an eval result by benchmark `name`.

  `step=None` (default) → return the post-training row (`step is None`)
  if present; else the last in-loop row for that benchmark.

  `step=\<int>` → return the exact in-loop row at that step (or
  `None` if no exact match — callers can do their own nearest-step
  lookup over `arm.evals`).

  <PySourceCode>
    ```python
    def eval(self, name: str, *, step: int | None = None) -> EvalResult | None:
        """Look up an eval result by benchmark ``name``.

        ``step=None`` (default) → return the post-training row (``step is None``)
        if present; else the last in-loop row for that benchmark.

        ``step=<int>`` → return the exact in-loop row at that step (or
        ``None`` if no exact match — callers can do their own nearest-step
        lookup over ``arm.evals``).
        """
        matches = [e for e in self.evals if e.name == name]
        if not matches:
            return None
        if step is None:
            post = [e for e in matches if e.step is None]
            if post:
                return post[0]
            in_loop = [e for e in matches if e.step is not None]
            return max(in_loop, key=lambda e: e.step) if in_loop else None
        return next((e for e in matches if e.step == step), None)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.experiment.EvalResult | None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;score&#x22;" type="&#x22;(self, metric) -> float | None&#x22;">
  Return the metric value for ranking by `success_metric`.

  Three forms supported:

  * `"pass_rate"` (bare) — looked up on `eval_metrics` (the
    primary post-training eval), then `metrics` (training-side).
  * `"bench.pass_rate"` (dotted) — looked up on
    `arm.eval("bench").metrics["pass_rate"]`. The dotted form wins
    when an experiment carries multiple named benchmarks and
    `success_metric` picks one explicitly.
  * Returns `None` if neither path resolves.

  <PySourceCode>
    ```python
    def score(self, metric: str) -> float | None:
        """Return the metric value for ranking by ``success_metric``.

        Three forms supported:

        * ``"pass_rate"`` (bare) — looked up on ``eval_metrics`` (the
          primary post-training eval), then ``metrics`` (training-side).
        * ``"bench.pass_rate"`` (dotted) — looked up on
          ``arm.eval("bench").metrics["pass_rate"]``. The dotted form wins
          when an experiment carries multiple named benchmarks and
          ``success_metric`` picks one explicitly.
        * Returns ``None`` if neither path resolves.
        """
        if "." in metric:
            bench_name, _, key = metric.partition(".")
            ev = self.eval(bench_name)
            if ev is not None and key in ev.metrics:
                return ev.metrics[key]
            return None
        if metric in self.eval_metrics:
            return self.eval_metrics[metric]
        return self.metrics.get(metric)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;metric&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;float | None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, name, run_config, status, metrics=dict(), eval_metrics=dict(), eval_breakdowns=dict(), evals=list(), run_id=None, error=None, train_seconds=None, eval_seconds=None, run_result=None, group_id=None, group_name=None) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;run_config&#x22;" type="&#x22;RunConfig&#x22;" value="null" />

    <PyParameter name="&#x22;status&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="&#x22;dict()&#x22;" />

    <PyParameter name="&#x22;eval_metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="&#x22;dict()&#x22;" />

    <PyParameter name="&#x22;eval_breakdowns&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;dict()&#x22;" />

    <PyParameter name="&#x22;evals&#x22;" type="&#x22;list[EvalResult]&#x22;" value="&#x22;list()&#x22;" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;error&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;train_seconds&#x22;" type="&#x22;float | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;eval_seconds&#x22;" type="&#x22;float | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;run_result&#x22;" type="&#x22;RunResult | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;group_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;group_name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
