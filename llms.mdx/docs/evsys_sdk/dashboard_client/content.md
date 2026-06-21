# dashboard_client (/docs/evsys_sdk/dashboard_client)



DashboardClient — push SDK runs to the EvolvingSystems backend, with a local mirror.

Architecture: SDK → Django HTTP → Supabase. The user holds an API key (the
same key the dashboard issues at `Settings → API keys`) plus a project id
(shared by everyone on the project, set in the SDK env). The SDK calls
`/api/dashboard/api/sdk/...` routes; the backend authenticates the key, checks
the user belongs to the project, and writes Supabase with its service key.

Robustness (wandb-offline style):

* Every write is **also** mirrored to a local folder (`EVSYS_LOG_DIR`,
  default `./evsys_sdk`). There is no remote-only mode.
* If the backend is unreachable (connection error / timeout / 5xx) the call
  logs a warning and keeps going with the local mirror — it does not crash.
* Auth is required by default: without an API key + project id the client
  raises. Set `EVSYS_OFFLINE=true` (or `offline=True`) to run with no
  auth, writing only to the local mirror.

Env vars (see constants.py): EVSYS\_API\_URL, EVSYS\_API\_KEY,
EVSYS\_PROJECT\_ID, EVSYS\_LOG\_DIR, EVSYS\_OFFLINE,
EVSYS\_LOGGING\_LEVEL.

Quick usage::

from evsys\_sdk import DashboardClient, ExperimentRun

client = DashboardClient()  # reads env vars

with ExperimentRun(client, experiment\_name="sft\_run\_v9",
recipe\_kind="sft",
run\_config=\{"lr": 1e-5, "batch\_size": 16}) as run:
for step in range(1, 1001):
run.log\_step(step, loss=...)
run.log\_eval(metrics=\{"pass\_at\_1": 0.83}, benchmark\_id="...")
run.set\_best\_score(0.83)

<PyAttribute name="&#x22;log&#x22;" type="null" value="&#x22;get_logger(__name__)&#x22;" />

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['DashboardClient', 'DashboardClientError', 'EvsysAuthError', 'ExperimentRun']&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;DashboardClientError&#x22;" href="&#x22;/docs/evsys_sdk/dashboard_client/DashboardClientError&#x22;" />

      <Card title="&#x22;EvsysAuthError&#x22;" href="&#x22;/docs/evsys_sdk/dashboard_client/EvsysAuthError&#x22;" />

      <Card title="&#x22;DashboardClient&#x22;" href="&#x22;/docs/evsys_sdk/dashboard_client/DashboardClient&#x22;" />

      <Card title="&#x22;ExperimentRun&#x22;" href="&#x22;/docs/evsys_sdk/dashboard_client/ExperimentRun&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;_new_id&#x22;" type="&#x22;() -> str&#x22;">
      <PySourceCode>
        ```python
        def _new_id() -> str:
            return str(uuid.uuid4())
        ```
      </PySourceCode>

      <PyFunctionReturn type="&#x22;str&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
