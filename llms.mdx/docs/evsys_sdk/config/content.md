# config (/docs/evsys_sdk/config)



Pydantic config models — the canonical YAML schema.

The full experiment is described by ExperimentConfig, which is what gets
serialized to and from YAML. Each block uses a `kind:` discriminator that
selects the matching registry entry; the registered class's `.Config` model
validates the rest of that block.

`extra='forbid'` is set everywhere so an evolutionary algorithm can't drop a
key by typoing it — the YAML loader will reject unknown fields loudly.

<Tabs items="[&#x22;Class&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;_Strict&#x22;" href="&#x22;/docs/evsys_sdk/config/_Strict&#x22;" />

      <Card title="&#x22;AlgorithmConfig&#x22;" href="&#x22;/docs/evsys_sdk/config/AlgorithmConfig&#x22;" />

      <Card title="&#x22;VerifierSpec&#x22;" href="&#x22;/docs/evsys_sdk/config/VerifierSpec&#x22;" />

      <Card title="&#x22;TransformSpec&#x22;" href="&#x22;/docs/evsys_sdk/config/TransformSpec&#x22;" />

      <Card title="&#x22;CallbackSpec&#x22;" href="&#x22;/docs/evsys_sdk/config/CallbackSpec&#x22;" />

      <Card title="&#x22;DataStoreSpec&#x22;" href="&#x22;/docs/evsys_sdk/config/DataStoreSpec&#x22;" />

      <Card title="&#x22;LogStoreSpec&#x22;" href="&#x22;/docs/evsys_sdk/config/LogStoreSpec&#x22;" />

      <Card title="&#x22;DataConfig&#x22;" href="&#x22;/docs/evsys_sdk/config/DataConfig&#x22;" />

      <Card title="&#x22;ModelConfig&#x22;" href="&#x22;/docs/evsys_sdk/config/ModelConfig&#x22;" />

      <Card title="&#x22;BackendConfig&#x22;" href="&#x22;/docs/evsys_sdk/config/BackendConfig&#x22;" />

      <Card title="&#x22;RunConfig&#x22;" href="&#x22;/docs/evsys_sdk/config/RunConfig&#x22;" />

      <Card title="&#x22;ExperimentConfig&#x22;" href="&#x22;/docs/evsys_sdk/config/ExperimentConfig&#x22;" />

      <Card title="&#x22;MatrixSpec&#x22;" href="&#x22;/docs/evsys_sdk/config/MatrixSpec&#x22;" />

      <Card title="&#x22;ContinualConfig&#x22;" href="&#x22;/docs/evsys_sdk/config/ContinualConfig&#x22;" />
    </Cards>
  </Tab>
</Tabs>
