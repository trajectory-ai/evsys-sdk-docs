# BaseAlgorithmConfig (/docs/evsys_sdk/algorithms/base/BaseAlgorithmConfig)



Fields common to every training algorithm.

Concrete algorithms subclass this and add their own knobs (SDFT: `topk`;
RL: `num_samples` / `verifier_name` / …). `extra="forbid"` so a YAML
typo fails loudly. A subclass may re-declare a field to change its default
(e.g. RL drops `learning_rate` to `1e-5`).

## Attributes [#attributes]

<PyAttribute name="&#x22;model_config&#x22;" type="null" value="&#x22;ConfigDict(extra='forbid')&#x22;" />

<PyAttribute name="&#x22;learning_rate&#x22;" type="&#x22;float&#x22;" value="&#x22;0.0001&#x22;" />

<PyAttribute name="&#x22;num_epochs&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />

<PyAttribute name="&#x22;batch_size&#x22;" type="&#x22;int&#x22;" value="&#x22;Field(default=4, gt=0)&#x22;" />

<PyAttribute name="&#x22;max_steps&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;">
  Hard step cap. When set, wins over `num_epochs * steps_per_epoch`.
</PyAttribute>

<PyAttribute name="&#x22;lora_rank&#x22;" type="&#x22;int&#x22;" value="&#x22;8&#x22;" />

<PyAttribute name="&#x22;renderer_name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;enable_thinking&#x22;" type="&#x22;bool | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;save_every&#x22;" type="&#x22;int&#x22;" value="&#x22;0&#x22;">
  If 0, computed from `save_at_fractions` (GCD-of-marks heuristic).
</PyAttribute>

<PyAttribute name="&#x22;save_at_fractions&#x22;" type="&#x22;list[float]&#x22;" value="&#x22;Field(default_factory=(lambda: [1.0]))&#x22;" />

<PyAttribute name="&#x22;callbacks&#x22;" type="&#x22;list[CallbackSpec]&#x22;" value="&#x22;Field(default_factory=list)&#x22;" />

<PyAttribute name="&#x22;adam_beta1&#x22;" type="&#x22;float&#x22;" value="&#x22;0.9&#x22;" />

<PyAttribute name="&#x22;adam_beta2&#x22;" type="&#x22;float&#x22;" value="&#x22;0.95&#x22;" />

<PyAttribute name="&#x22;adam_eps&#x22;" type="&#x22;float&#x22;" value="&#x22;1e-08&#x22;" />

<PyAttribute name="&#x22;wandb_project&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;wandb_name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
