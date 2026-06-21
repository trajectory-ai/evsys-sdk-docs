# PassRate (/docs/evsys_sdk/metrics/basic/PassRate)



Micro pass rate: passing samples / total samples across all tasks.

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'pass_rate'&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;compute&#x22;" type="&#x22;(self, task_rewards) -> float&#x22;">
  <PySourceCode>
    ```python
    def compute(self, task_rewards: Sequence[Sequence[float]]) -> float:
        passes = sum(1 for rs in task_rewards for r in rs if _passes(r))
        total = sum(len(rs) for rs in task_rewards)
        return passes / total if total else 0.0
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;task_rewards&#x22;" type="&#x22;Sequence[Sequence[float]]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;float&#x22;" />
</PyFunction>
