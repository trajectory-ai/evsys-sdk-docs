# runner (/docs/evsys_sdk/eval/runner)



High-level eval runner: loads dataset, runs model eval, computes summary.

Generic, domain-agnostic. Project-specific eval harnesses (e.g. an API search
eval) live in their own repos and reuse this infra (`score_rows`, `AliasMatcher`,
`load_eval_dataset`, …).

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;EvalArtifacts&#x22;" href="&#x22;/docs/evsys_sdk/eval/runner/EvalArtifacts&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;load_eval_dataset&#x22;" type="&#x22;(path) -> list[dict[str, Any]]&#x22;">
      Load an eval JSON file. Supports both:

      * list of rows `[\{tool_slug, toolkit, queries\}, ...]` (v2 shape), or
      * dict with `results: [...]` (older 3-query shape).

      <PySourceCode>
        ```python
        def load_eval_dataset(path: str | Path) -> list[dict[str, Any]]:
            """Load an eval JSON file. Supports both:
              * list of rows `[{tool_slug, toolkit, queries}, ...]` (v2 shape), or
              * dict with `results: [...]` (older 3-query shape)."""
            data = json.loads(Path(path).read_text())
            if isinstance(data, dict) and "results" in data:
                return [
                    {
                        "tool_slug": r.get("tool_slug"),
                        "toolkit": r.get("toolkit"),
                        "queries": [q.get("query") for q in r.get("queries", [])],
                    }
                    for r in data["results"]
                ]
            return data
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;path&#x22;" type="&#x22;str | Path&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;list[dict[str, typing.Any]]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;evaluate_model&#x22;" type="&#x22;(*, dataset_path, aliases_path, client, secondary_aliases_path=None, config=None, output_dir=None, progress=True) -> EvalArtifacts&#x22;">
      <PySourceCode>
        ```python
        def evaluate_model(
            *,
            dataset_path: str | Path,
            aliases_path: str | Path,
            client: InferenceClient,
            secondary_aliases_path: str | Path | None = None,
            config: ModelEvalConfig | None = None,
            output_dir: str | Path | None = None,
            progress: bool = True,
        ) -> EvalArtifacts:
            rows = load_eval_dataset(dataset_path)
            matcher = AliasMatcher.from_files(aliases_path, secondary_aliases_path)
            result: ModelEvalResult = run_model_eval(
                rows, client=client, config=config, progress=progress
            )

            summary = score_rows(
                result.rows,
                matcher=matcher,
                query_found_fn=model_query_found,
                retry_report=result.retry_report,
            )
            artifacts = EvalArtifacts(
                summary=summary,
                summary_strict_primary=None,
                per_row_results=result.rows,
            )

            if output_dir is not None:
                _write_artifacts(artifacts, output_dir, kind="model")
            return artifacts
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;dataset_path&#x22;" type="&#x22;str | Path&#x22;" value="null" />

        <PyParameter name="&#x22;aliases_path&#x22;" type="&#x22;str | Path&#x22;" value="null" />

        <PyParameter name="&#x22;client&#x22;" type="&#x22;InferenceClient&#x22;" value="null" />

        <PyParameter name="&#x22;secondary_aliases_path&#x22;" type="&#x22;str | Path | None&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;config&#x22;" type="&#x22;ModelEvalConfig | None&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;output_dir&#x22;" type="&#x22;str | Path | None&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;progress&#x22;" type="&#x22;bool&#x22;" value="&#x22;True&#x22;" />
      </div>

      <PyFunctionReturn type="&#x22;evsys_sdk.eval.runner.EvalArtifacts&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_write_artifacts&#x22;" type="&#x22;(artifacts, output_dir, *, kind) -> None&#x22;">
      <PySourceCode>
        ```python
        def _write_artifacts(artifacts: EvalArtifacts, output_dir: str | Path, *, kind: str) -> None:
            out = Path(output_dir)
            out.mkdir(parents=True, exist_ok=True)

            summary_path = out / f"{kind}_summary.json"
            summary_path.write_text(json.dumps(artifacts.summary.as_dict(), indent=2))

            md_path = out / f"{kind}_summary.md"
            md_path.write_text(format_summary_markdown(artifacts.summary, title=f"{kind} eval"))

            rows_path = out / f"{kind}_per_row.json"
            rows_path.write_text(json.dumps(artifacts.per_row_results, indent=2))

            if artifacts.summary_strict_primary:
                strict_path = out / f"{kind}_summary_strict_primary.json"
                strict_path.write_text(json.dumps(artifacts.summary_strict_primary.as_dict(), indent=2))
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;artifacts&#x22;" type="&#x22;EvalArtifacts&#x22;" value="null" />

        <PyParameter name="&#x22;output_dir&#x22;" type="&#x22;str | Path&#x22;" value="null" />

        <PyParameter name="&#x22;kind&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;None&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
