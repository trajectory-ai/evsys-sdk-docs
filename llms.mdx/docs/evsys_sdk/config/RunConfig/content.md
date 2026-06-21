# RunConfig (/docs/evsys_sdk/config/RunConfig)



A single training run (one cell in a campaign matrix).

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null">
  Human-readable name; unique within the experiment.
</PyAttribute>

<PyAttribute name="&#x22;data&#x22;" type="&#x22;DataConfig&#x22;" value="null" />

<PyAttribute name="&#x22;model&#x22;" type="&#x22;ModelConfig&#x22;" value="null" />

<PyAttribute name="&#x22;algorithm&#x22;" type="&#x22;AlgorithmConfig&#x22;" value="null" />

<PyAttribute name="&#x22;backend&#x22;" type="&#x22;BackendConfig&#x22;" value="&#x22;Field(default_factory=BackendConfig)&#x22;" />

<PyAttribute name="&#x22;seed&#x22;" type="&#x22;int&#x22;" value="&#x22;42&#x22;" />

<PyAttribute name="&#x22;tags&#x22;" type="&#x22;list[str]&#x22;" value="&#x22;Field(default_factory=list)&#x22;" />
