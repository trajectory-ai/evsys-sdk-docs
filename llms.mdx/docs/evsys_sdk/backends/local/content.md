# local (/docs/evsys_sdk/backends/local)



LocalBackend — TRL-based local training (transformers + peft + trl).

prepare() loads tokenizer + model + (optional) LoRA. Heavy lifting (the
TRL trainer loop) is in algorithms/local\_\*.py.

<Tabs items="[&#x22;Class&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;LocalBackendConfig&#x22;" href="&#x22;/docs/evsys_sdk/backends/local/LocalBackendConfig&#x22;" />

      <Card title="&#x22;LocalBackend&#x22;" href="&#x22;/docs/evsys_sdk/backends/local/LocalBackend&#x22;" />
    </Cards>
  </Tab>
</Tabs>
