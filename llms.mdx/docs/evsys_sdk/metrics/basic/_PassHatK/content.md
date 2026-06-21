# _PassHatK (/docs/evsys_sdk/metrics/basic/_PassHatK)



pass^k: a task is solved only if **all** of its first `k` samples pass.

## Attributes [#attributes]

<PyAttribute name="&#x22;k&#x22;" type="&#x22;int&#x22;" value="null" />

## Functions [#functions]

<PyFunction name="&#x22;compute&#x22;" type="&#x22;(self, task_rewards) -> float&#x22;">
  <PySourceCode>
    ```python
    def compute(self, task_rewards: Sequence[Sequence[float]]) -> float:
        tasks = _nonempty(task_rewards)
        if not tasks:
            return 0.0
        solved = sum(1 for rs in tasks if all(_passes(r) for r in rs[: self.k]))
        return solved / len(tasks)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;task_rewards&#x22;" type="&#x22;Sequence[Sequence[float]]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;float&#x22;" />
</PyFunction>
