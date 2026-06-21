# multiplex (/docs/evsys_sdk/log_stores/multiplex)



MultiplexLogStore — fan out to several log stores at once.

Useful for `log_store.kind: multiplex` configs:

log\_store:
kind: multiplex
params:
children:

* kind: jsonl
  params: \{ log\_dir: ./logs }
* kind: tensorboard
  params: \{ log\_dir: ./tb }

<Tabs items="[&#x22;Class&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;MultiplexLogStoreConfig&#x22;" href="&#x22;/docs/evsys_sdk/log_stores/multiplex/MultiplexLogStoreConfig&#x22;" />

      <Card title="&#x22;MultiplexLogStore&#x22;" href="&#x22;/docs/evsys_sdk/log_stores/multiplex/MultiplexLogStore&#x22;" />
    </Cards>
  </Tab>
</Tabs>
