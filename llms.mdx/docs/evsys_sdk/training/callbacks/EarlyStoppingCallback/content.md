# EarlyStoppingCallback (/docs/evsys_sdk/training/callbacks/EarlyStoppingCallback)



Watch a metric emitted on eval; request\_stop after N evals without
improvement.

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'early_stopping'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;EarlyStoppingConfig&#x22;" />

<PyAttribute name="&#x22;metric&#x22;" type="&#x22;str&#x22;" value="null" />

<PyAttribute name="&#x22;eval_name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;patience&#x22;" type="&#x22;int&#x22;" value="&#x22;3&#x22;" />

<PyAttribute name="&#x22;mode&#x22;" type="&#x22;str&#x22;" value="&#x22;'max'&#x22;" />

<PyAttribute name="&#x22;min_delta&#x22;" type="&#x22;float&#x22;" value="&#x22;0.0&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;on_eval&#x22;" type="&#x22;(self, state, step_idx, eval_name, metrics)&#x22;">
  <PySourceCode>
    ```python
    def on_eval(self, state, step_idx, eval_name, metrics):
        if self.eval_name is not None and eval_name != self.eval_name:
            return
        if self.metric not in metrics:
            return
        v = float(metrics[self.metric])
        if self._best is None:
            self._best = v
            return
        improved = (
            v > self._best + self.min_delta if self.mode == "max"
            else v < self._best - self.min_delta
        )
        if improved:
            self._best = v
            self._staleness = 0
        else:
            self._staleness += 1
            if self._staleness >= self.patience:
                logger.info(
                    "EarlyStoppingCallback: stop after %d evals without improvement on %r",
                    self._staleness, self.metric,
                )
                state.request_stop()
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

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, metric, eval_name=None, patience=3, mode='max', min_delta=0.0) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;metric&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;eval_name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;patience&#x22;" type="&#x22;int&#x22;" value="&#x22;3&#x22;" />

    <PyParameter name="&#x22;mode&#x22;" type="&#x22;str&#x22;" value="&#x22;'max'&#x22;" />

    <PyParameter name="&#x22;min_delta&#x22;" type="&#x22;float&#x22;" value="&#x22;0.0&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
