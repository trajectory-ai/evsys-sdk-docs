# algorithms (/docs/evsys_sdk/algorithms)



Built-in algorithms.

Each algorithm targets a specific (recipe-kind, backend-kind) pair via the
registry: the algorithm's `name` is what users put in `algorithm.kind:` of
the YAML; routing to a specific backend happens inside the algorithm's
.train() (it inspects ctx.backend.name).

Some algorithms are backend-agnostic (Mock); most route to a single backend.

<Tabs items="[&#x22;Modules&#x22;]">
  <Tab value="&#x22;Modules&#x22;">
    <Cards>
      <Card href="&#x22;/docs/evsys_sdk/algorithms/mock_rl&#x22;" title="&#x22;mock_rl&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/algorithms/sdft&#x22;" title="&#x22;sdft&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/algorithms/rl&#x22;" title="&#x22;rl&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/algorithms/sft&#x22;" title="&#x22;sft&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/algorithms/combo&#x22;" title="&#x22;combo&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/algorithms/local_rl&#x22;" title="&#x22;local_rl&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/algorithms/local_sft&#x22;" title="&#x22;local_sft&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/algorithms/gepa_prompt&#x22;" title="&#x22;gepa_prompt&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/algorithms/base&#x22;" title="&#x22;base&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/algorithms/mock_sft&#x22;" title="&#x22;mock_sft&#x22;" />
    </Cards>
  </Tab>
</Tabs>
