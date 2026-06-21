# trajectory (/docs/evsys_sdk/training/trajectory)



The rollout data model — one shape for all (multi-turn) rollouts.

Every rollout in the SDK — single- or multi-turn, RL or SDFT — is a
:class:`Trajectory`: an ordered list of :class:`Turn`\s (each a prompt + a
sampled completion with per-token logprobs) plus a scalar reward. Harbor's
`RolloutDetail` (ATIF) is converted into this at the engine boundary
(:func:`evsys_sdk.training.harbor_engine.run_harbor_rollouts`), so the rest of
the SDK only ever sees `Trajectory`. The IS-loss data prep
(:mod:`evsys_sdk.training.data_processing`) emits one `Datum` per turn.

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['Turn', 'Trajectory', 'TrajectoryGroup']&#x22;" />

<Tabs items="[&#x22;Class&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;Turn&#x22;" href="&#x22;/docs/evsys_sdk/training/trajectory/Turn&#x22;" />

      <Card title="&#x22;Trajectory&#x22;" href="&#x22;/docs/evsys_sdk/training/trajectory/Trajectory&#x22;" />

      <Card title="&#x22;TrajectoryGroup&#x22;" href="&#x22;/docs/evsys_sdk/training/trajectory/TrajectoryGroup&#x22;" />
    </Cards>
  </Tab>
</Tabs>
