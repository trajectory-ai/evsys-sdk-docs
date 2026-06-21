# tinker (/docs/evsys_sdk/backends/tinker)



TinkerBackend — thin wrapper around the tinker SDK.

prepare() lazily creates a ServiceClient and returns it as a handle along with
the chosen model name + a tokenizer. The actual training-client creation is
left to the algorithm so it can pick LoraConfig / lr / etc.

Most of the heavy lifting (SFT loop, RL loop, checkpointing, eval cadence) is
in tinker\_cookbook — algorithms that target this backend should call into the
cookbook recipes rather than reinventing the loop.

<Tabs items="[&#x22;Class&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;TinkerBackendConfig&#x22;" href="&#x22;/docs/evsys_sdk/backends/tinker/TinkerBackendConfig&#x22;" />

      <Card title="&#x22;TinkerBackend&#x22;" href="&#x22;/docs/evsys_sdk/backends/tinker/TinkerBackend&#x22;" />
    </Cards>
  </Tab>
</Tabs>
