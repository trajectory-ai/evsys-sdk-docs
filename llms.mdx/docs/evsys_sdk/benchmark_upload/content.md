# benchmark_upload (/docs/evsys_sdk/benchmark_upload)



Register a local harbor-format benchmark with the dashboard.

Each `data/benchmark/\<name>/` directory becomes a row in the dashboard's
benchmark table so experiments can reference it by id. Re-uploading the
same content is a no-op (we hash `tasks.jsonl`); re-uploading changed
content registers a new version.

CLI entry point: `evsys benchmark upload data/benchmark/\<name>`.
Programmatic: `upload_benchmark(store, path)`.

The harbor upload body lives in `_harbor_upload.upload_harbor` (reusable for
other harbor-format entities) — this module only wires the benchmark store
methods and keeps the historical `UploadResult` shape.

<PyAttribute name="&#x22;BENCHMARK_FORMAT&#x22;" type="null" value="&#x22;HARBOR_FORMAT&#x22;" />

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['BENCHMARK_FORMAT', 'UploadResult', 'upload_benchmark']&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;UploadResult&#x22;" href="&#x22;/docs/evsys_sdk/benchmark_upload/UploadResult&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;upload_benchmark&#x22;" type="&#x22;(store, path) -> UploadResult&#x22;">
      Upload (or re-upload) a harbor benchmark directory.

      See `_harbor_upload.upload_harbor` for the shared idempotency logic.

      <PySourceCode>
        ```python
        def upload_benchmark(store: Any, path: str | Path) -> UploadResult:
            """Upload (or re-upload) a harbor benchmark directory.

            See ``_harbor_upload.upload_harbor`` for the shared idempotency logic.
            """
            result = upload_harbor(
                path,
                create_record=store.create_benchmark,
                add_rows=store.add_benchmark_rows,
                list_existing=store.list_benchmarks,
                format=BENCHMARK_FORMAT,
            )
            return UploadResult(
                benchmark_id=result.id,
                name=result.name,
                version=result.version,
                content_hash=result.content_hash,
                status=result.status,
                n_tasks=result.n_tasks,
            )
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;store&#x22;" type="&#x22;Any&#x22;" value="null" />

        <PyParameter name="&#x22;path&#x22;" type="&#x22;str | Path&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;evsys_sdk.benchmark_upload.UploadResult&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
