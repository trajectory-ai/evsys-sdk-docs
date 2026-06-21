# ComboPhaseConfig (/docs/evsys_sdk/algorithms/combo/ComboPhaseConfig)



## Attributes [#attributes]

<PyAttribute name="&#x22;model_config&#x22;" type="null" value="&#x22;ConfigDict(extra='forbid')&#x22;" />

<PyAttribute name="&#x22;kind&#x22;" type="&#x22;str&#x22;" value="null">
  Registry name of the sub-algorithm to run (e.g. 'mock\_sft', 'rl').
</PyAttribute>

<PyAttribute name="&#x22;config&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;Field(default_factory=dict)&#x22;">
  Per-phase config dict passed to the sub-algorithm's Config constructor.
</PyAttribute>

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  Optional human-readable phase name; defaults to `phase\{i\}_\{kind\}`.
</PyAttribute>
