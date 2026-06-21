# MeanReward (/docs/evsys_sdk/metrics/basic/MeanReward)



Macro mean reward: mean over tasks of each task's mean sample reward.

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'mean_reward'&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;compute&#x22;" type="&#x22;(self, task_rewards) -> float&#x22;">
  <PySourceCode>
    ```python
    def compute(self, task_rewards: Sequence[Sequence[float]]) -> float:
        tasks = _nonempty(task_rewards)
        if not tasks:
            return 0.0
        return sum(sum(rs) / len(rs) for rs in tasks) / len(tasks)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;task_rewards&#x22;" type="&#x22;Sequence[Sequence[float]]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;float&#x22;" />
</PyFunction>
