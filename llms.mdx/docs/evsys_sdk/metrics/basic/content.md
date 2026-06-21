# basic (/docs/evsys_sdk/metrics/basic)



Built-in benchmark metrics — reduce per-task rollout rewards to a scalar.

A benchmark scores each task by running its verifier on `num_samples` rollouts,
yielding a list of per-sample rewards per task. A **metric** reduces that
`list[list[float]]` (one inner list per task, holding that task's sample
rewards) to a single number. Metrics are referenced by **string name** on a
benchmark's `metrics:` list and registered with `@register_metric`; add your
own the same way in a project.

Built-ins:

* `mean_reward` / `avg` — macro mean reward (mean over tasks of each task's
  mean sample reward).
* `pass_rate` — micro pass rate (passing samples / total samples, pooled).
* `pass@k` — a task is solved if **any** of its first `k` samples passes.
* `pass^k` — a task is solved only if **all** of its first `k` samples pass
  (consistency / "pass-hat-k").

The interface is one method::

def compute(self, task\_rewards: Sequence\[Sequence\[float]]) -> float

<PyAttribute name="&#x22;PASS_THRESHOLD&#x22;" type="null" value="&#x22;1.0&#x22;" />

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['MeanReward', 'Avg', 'PassRate', 'PassAt1', 'PassAt3', 'PassHat3']&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;MeanReward&#x22;" href="&#x22;/docs/evsys_sdk/metrics/basic/MeanReward&#x22;" />

      <Card title="&#x22;Avg&#x22;" href="&#x22;/docs/evsys_sdk/metrics/basic/Avg&#x22;" />

      <Card title="&#x22;PassRate&#x22;" href="&#x22;/docs/evsys_sdk/metrics/basic/PassRate&#x22;" />

      <Card title="&#x22;_PassAtK&#x22;" href="&#x22;/docs/evsys_sdk/metrics/basic/_PassAtK&#x22;" />

      <Card title="&#x22;_PassHatK&#x22;" href="&#x22;/docs/evsys_sdk/metrics/basic/_PassHatK&#x22;" />

      <Card title="&#x22;PassAt1&#x22;" href="&#x22;/docs/evsys_sdk/metrics/basic/PassAt1&#x22;" />

      <Card title="&#x22;PassAt3&#x22;" href="&#x22;/docs/evsys_sdk/metrics/basic/PassAt3&#x22;" />

      <Card title="&#x22;PassHat3&#x22;" href="&#x22;/docs/evsys_sdk/metrics/basic/PassHat3&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;_passes&#x22;" type="&#x22;(reward) -> bool&#x22;">
      <PySourceCode>
        ```python
        def _passes(reward: float) -> bool:
            return reward >= PASS_THRESHOLD
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;reward&#x22;" type="&#x22;float&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;bool&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_nonempty&#x22;" type="&#x22;(task_rewards) -> list[Sequence[float]]&#x22;">
      <PySourceCode>
        ```python
        def _nonempty(task_rewards: Sequence[Sequence[float]]) -> list[Sequence[float]]:
            return [rs for rs in task_rewards if rs]
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;task_rewards&#x22;" type="&#x22;Sequence[Sequence[float]]&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;list[typing.Sequence[float]]&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
