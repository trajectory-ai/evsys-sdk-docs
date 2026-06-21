# store (/docs/evsys_sdk/store)



EvsysStore — HTTP client for the project → goals/datasets/benchmarks →
experiments → groups → runs → checkpoints/evals/metrics hierarchy.

The SDK holds **no Supabase service key**. Every call routes through the
backend gateway (`POST \{API\}/api/dashboard/api/sdk/data/`) authenticated by the
user's Bearer API key; the backend validates the key, checks the user is a
member of the relevant project, runs the op with its service key, and returns
the data. Same method surface as before — only the transport changed.

Env: `EVSYS_API_URL` (backend base URL), `EVSYS_API_KEY` (Bearer),
`EVSYS_PROJECT_ID` (default project for project-scoped ops).

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['EvsysStore', 'EvsysStoreError']&#x22;" />

<Tabs items="[&#x22;Class&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;EvsysStoreError&#x22;" href="&#x22;/docs/evsys_sdk/store/EvsysStoreError&#x22;" />

      <Card title="&#x22;EvsysStore&#x22;" href="&#x22;/docs/evsys_sdk/store/EvsysStore&#x22;" />
    </Cards>
  </Tab>
</Tabs>
