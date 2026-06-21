# Evaluator (/docs/evsys_sdk/training/loop/Evaluator)



Periodic in-loop evaluator.

Each evaluator owns one metric source (a Benchmark, a custom probe,
etc.) and carries its own `run_every` cadence. This lets a single
training run mix a fast val benchmark (`run_every: 50`) with a
heavier test benchmark (`run_every: 500`).

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null">
  Short name; used to prefix the metric keys (`val/\<name>/\<key>`).
</PyAttribute>

<PyAttribute name="&#x22;run_every&#x22;" type="&#x22;int&#x22;" value="null">
  Per-evaluator step cadence. `0` → disabled (never fires).
  Positive → fire when `(step + 1) % run_every == 0`.
</PyAttribute>

## Functions [#functions]

<PyFunction name="&#x22;evaluate&#x22;" type="&#x22;(self, sampler, *, model_path=None, step=None) -> dict[str, float]&#x22;">
  <PySourceCode>
    ```python
    async def evaluate(
        self, sampler: SamplingClient, *,
        model_path: str | None = None, step: int | None = None,
    ) -> dict[str, float]:
        ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;sampler&#x22;" type="&#x22;SamplingClient&#x22;" value="null" />

    <PyParameter name="&#x22;model_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict[str, float]&#x22;" />
</PyFunction>
