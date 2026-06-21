# protocols (/docs/evsys_sdk/protocols)



Protocol contracts for every extension point.

We use `typing.Protocol` (PEP 544) instead of ABCs so extensions don't have
to subclass anything from the library — any class that implements the methods
satisfies the protocol. This is what makes third-party algorithms / verifiers
/ metrics trivial to add.

A few protocols are runtime-checkable so the registry can give better error
messages, but most are duck-typed.

<Tabs items="[&#x22;Class&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;RunContext&#x22;" href="&#x22;/docs/evsys_sdk/protocols/RunContext&#x22;" />

      <Card title="&#x22;RunResult&#x22;" href="&#x22;/docs/evsys_sdk/protocols/RunResult&#x22;" />

      <Card title="&#x22;Algorithm&#x22;" href="&#x22;/docs/evsys_sdk/protocols/Algorithm&#x22;" />

      <Card title="&#x22;VerificationResult&#x22;" href="&#x22;/docs/evsys_sdk/protocols/VerificationResult&#x22;" />

      <Card title="&#x22;Verifier&#x22;" href="&#x22;/docs/evsys_sdk/protocols/Verifier&#x22;" />

      <Card title="&#x22;Metric&#x22;" href="&#x22;/docs/evsys_sdk/protocols/Metric&#x22;" />

      <Card title="&#x22;DataStore&#x22;" href="&#x22;/docs/evsys_sdk/protocols/DataStore&#x22;" />

      <Card title="&#x22;LogStore&#x22;" href="&#x22;/docs/evsys_sdk/protocols/LogStore&#x22;" />

      <Card title="&#x22;Backend&#x22;" href="&#x22;/docs/evsys_sdk/protocols/Backend&#x22;" />

      <Card title="&#x22;InferenceClient&#x22;" href="&#x22;/docs/evsys_sdk/protocols/InferenceClient&#x22;" />

      <Card title="&#x22;Transform&#x22;" href="&#x22;/docs/evsys_sdk/protocols/Transform&#x22;" />
    </Cards>
  </Tab>
</Tabs>
