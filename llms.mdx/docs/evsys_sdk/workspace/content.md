# workspace (/docs/evsys_sdk/workspace)



Workspace — local cache for remote datasets/benchmarks.

Remote-first: datasets live in the backend (D20, accessed via `EvsysStore`
over the gateway). Streaming every row over HTTP during training is slow, so the
agent materializes a dataset to a local JSONL **once** and trains from the local
file. On `pull_dataset` the local copy is reused if present and complete;
otherwise it's fetched from remote, written, and cached.

Safe to cache: datasets are versioned and immutable per version, so a given
`dataset_id` never changes — the only risk is a partial pull, guarded by a
`.meta.json` manifest (atomic rename + `complete` flag + n\_rows match).

The workspace root (`$EVSYS_WORKSPACE` or `./.evsys`) writes a
self-ignoring `.gitignore` (`*`) on init, so nothing in it is ever tracked.
Rows are written **raw** (D17); `MaterializedDataset` carries the dataset's
`format` + `transform` so the trainer can render typed rows on read.

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['Workspace', 'MaterializedDataset', 'read_jsonl_rows']&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;MaterializedDataset&#x22;" href="&#x22;/docs/evsys_sdk/workspace/MaterializedDataset&#x22;" />

      <Card title="&#x22;Workspace&#x22;" href="&#x22;/docs/evsys_sdk/workspace/Workspace&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;read_jsonl_rows&#x22;" type="&#x22;(path) -> list[dict[str, Any]]&#x22;">
      Read a materialized `.evsys/` JSONL (one payload per line) to dicts.

      <PySourceCode>
        ```python
        def read_jsonl_rows(path: str) -> list[dict[str, Any]]:
            """Read a materialized ``.evsys/`` JSONL (one payload per line) to dicts."""
            out: list[dict[str, Any]] = []
            for line in Path(path).read_text().splitlines():
                line = line.strip()
                if line:
                    out.append(json.loads(line))
            return out
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;path&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;list[dict[str, typing.Any]]&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
