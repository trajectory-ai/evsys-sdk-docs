# AlgorithmConfig (/docs/evsys_sdk/config/AlgorithmConfig)



Training algorithm (recipe) — one of the registered @register\_algorithm.

## Attributes [#attributes]

<PyAttribute name="&#x22;kind&#x22;" type="&#x22;str&#x22;" value="null">
  Registry key, e.g. 'sft', 'rl\_grpo', 'dpo'.
</PyAttribute>

<PyAttribute name="&#x22;params&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;Field(default_factory=dict)&#x22;">
  Algorithm-specific parameters; validated against \<Algorithm>.Config.
</PyAttribute>
