# rl (/docs/evsys_sdk/algorithms/rl)



RL — on-policy reinforcement learning, rollouts run by harbor's engine.

Rollouts are handed to **harbor's `Job` engine** (retries, bounded
concurrency, timeouts, persistence) via
:func:`evsys_sdk.training.harbor_engine.run_harbor_rollouts`; this algorithm
just turns `HarborTask` rows into the engine's inputs and assembles the
returned trajectories into importance-sampling-loss Datums.

Composer plumbing lives in :class:`~evsys_sdk.algorithms.base.BaseAlgorithm`;
RL supplies:

* :meth:`setup` — parse `HarborTask` rows; stash the backend + the
  `.evsys` rollout workspace.
* :meth:`build_batch` — save a sampler checkpoint (on-policy), roll out the
  batch via harbor, group-normalize advantages, emit IS-loss Datums.

The agent harness is pluggable: by default harbor runs our `BasicLoopAgent`
(`Chat(TinkerLLM)`); set `agent_import_path` to any harbor `BaseAgent`.

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['RL', 'RLConfig']&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;RLConfig&#x22;" href="&#x22;/docs/evsys_sdk/algorithms/rl/RLConfig&#x22;" />

      <Card title="&#x22;RL&#x22;" href="&#x22;/docs/evsys_sdk/algorithms/rl/RL&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;_all_equal&#x22;" type="&#x22;(xs) -> bool&#x22;">
      <PySourceCode>
        ```python
        def _all_equal(xs: list[float]) -> bool:
            return len(xs) > 0 and all(x == xs[0] for x in xs)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;xs&#x22;" type="&#x22;list[float]&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;bool&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
