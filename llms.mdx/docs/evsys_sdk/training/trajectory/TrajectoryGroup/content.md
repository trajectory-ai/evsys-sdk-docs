# TrajectoryGroup (/docs/evsys_sdk/training/trajectory/TrajectoryGroup)



All rollouts sampled from one task (`num_samples` of them). The
group-relative advantage baseline subtracts the within-group mean reward.

## Attributes [#attributes]

<PyAttribute name="&#x22;trajectories&#x22;" type="&#x22;list[Trajectory]&#x22;" value="null" />

<PyAttribute name="&#x22;rewards&#x22;" type="&#x22;list[float]&#x22;" value="null" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, trajectories) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;trajectories&#x22;" type="&#x22;list[Trajectory]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
