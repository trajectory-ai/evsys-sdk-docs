# PrintProgressCallback (/docs/evsys_sdk/training/callbacks/PrintProgressCallback)



Compact one-liner per step to stdout. Useful when you're not on a
dashboard.

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'print_progress'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;PrintProgressConfig&#x22;" />

<PyAttribute name="&#x22;every&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />

<PyAttribute name="&#x22;keys&#x22;" type="&#x22;list[str] | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;stream&#x22;" type="&#x22;Any&#x22;" value="&#x22;field(default_factory=(lambda: sys.stdout))&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;on_step_end&#x22;" type="&#x22;(self, state, step_idx, batch, metrics)&#x22;">
  <PySourceCode>
    ```python
    def on_step_end(self, state, step_idx, batch, metrics):
        if self.every > 1 and (step_idx % self.every) != 0:
            return
        view = (
            {k: metrics[k] for k in self.keys if k in metrics}
            if self.keys is not None else metrics
        )
        line = f"[{step_idx + 1}/{state.num_steps}] " + " ".join(
            f"{k}={_fmt_value(v)}" for k, v in view.items()
        )
        print(line, file=self.stream, flush=True)
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

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, every=1, keys=None, stream=(lambda: sys.stdout)()) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;every&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />

    <PyParameter name="&#x22;keys&#x22;" type="&#x22;list[str] | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;stream&#x22;" type="&#x22;Any&#x22;" value="&#x22;(lambda: sys.stdout)()&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
