# EvalArtifacts (/docs/evsys_sdk/eval/runner/EvalArtifacts)



Final outputs of an eval run.

## Attributes [#attributes]

<PyAttribute name="&#x22;summary&#x22;" type="&#x22;EvalSummary&#x22;" value="null" />

<PyAttribute name="&#x22;summary_strict_primary&#x22;" type="&#x22;EvalSummary | None&#x22;" value="null">
  Optional strict variant (e.g. primary-only). None for plain model evals.
</PyAttribute>

<PyAttribute name="&#x22;per_row_results&#x22;" type="&#x22;list[dict[str, Any]]&#x22;" value="null">
  Raw per-row, per-query results so downstream tooling can re-score.
</PyAttribute>

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, summary, summary_strict_primary, per_row_results) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;summary&#x22;" type="&#x22;EvalSummary&#x22;" value="null" />

    <PyParameter name="&#x22;summary_strict_primary&#x22;" type="&#x22;EvalSummary | None&#x22;" value="null" />

    <PyParameter name="&#x22;per_row_results&#x22;" type="&#x22;list[dict[str, Any]]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
