# RLConfig (/docs/evsys_sdk/algorithms/rl/RLConfig)



Config for :class:`RL` — shared knobs from :class:`BaseAlgorithmConfig`
plus RL/rollout-engine fields.

## Attributes [#attributes]

<PyAttribute name="&#x22;learning_rate&#x22;" type="&#x22;float&#x22;" value="&#x22;1e-05&#x22;">
  RL needs a lower LR than SFT — IS gradients can be large.
</PyAttribute>

<PyAttribute name="&#x22;num_samples&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;">
  Rollouts per task (`group_size`); >= 2 enables the advantage baseline.
</PyAttribute>

<PyAttribute name="&#x22;verifier_name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  Fallback verifier-fn name when a HarborTask's InProcessVerifier leaves
  `fn_name` empty (normally the verifier rides per-row on the task).
</PyAttribute>

<PyAttribute name="&#x22;max_tokens&#x22;" type="&#x22;int&#x22;" value="&#x22;256&#x22;" />

<PyAttribute name="&#x22;temperature&#x22;" type="&#x22;float&#x22;" value="&#x22;1.0&#x22;" />

<PyAttribute name="&#x22;max_turns&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />

<PyAttribute name="&#x22;drop_constant_reward&#x22;" type="&#x22;bool&#x22;" value="&#x22;True&#x22;" />

<PyAttribute name="&#x22;system_prompt&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;user_template&#x22;" type="&#x22;str&#x22;" value="&#x22;'{prompt}'&#x22;" />

<PyAttribute name="&#x22;agent_import_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  Override the rollout agent harness (any harbor `BaseAgent` import path).
  Default: our `BasicLoopAgent`.
</PyAttribute>

<PyAttribute name="&#x22;n_concurrent&#x22;" type="&#x22;int&#x22;" value="&#x22;4&#x22;" />

<PyAttribute name="&#x22;max_retries&#x22;" type="&#x22;int&#x22;" value="&#x22;2&#x22;" />
