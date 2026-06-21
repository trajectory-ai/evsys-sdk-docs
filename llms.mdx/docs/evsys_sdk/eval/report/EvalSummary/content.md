# EvalSummary (/docs/evsys_sdk/eval/report/EvalSummary)



## Attributes [#attributes]

<PyAttribute name="&#x22;pass_at_1&#x22;" type="&#x22;float&#x22;" value="null" />

<PyAttribute name="&#x22;pass_at_3&#x22;" type="&#x22;float&#x22;" value="null" />

<PyAttribute name="&#x22;pass_pow_3&#x22;" type="&#x22;float&#x22;" value="null" />

<PyAttribute name="&#x22;n_rows&#x22;" type="&#x22;int&#x22;" value="null" />

<PyAttribute name="&#x22;n_queries&#x22;" type="&#x22;int&#x22;" value="null" />

<PyAttribute name="&#x22;per_toolkit&#x22;" type="&#x22;dict[str, dict[str, float]]&#x22;" value="&#x22;field(default_factory=dict)&#x22;" />

<PyAttribute name="&#x22;retry_report&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;field(default_factory=dict)&#x22;" />

<PyAttribute name="&#x22;notes&#x22;" type="&#x22;list[str]&#x22;" value="&#x22;field(default_factory=list)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;as_dict&#x22;" type="&#x22;(self) -> dict[str, Any]&#x22;">
  <PySourceCode>
    ```python
    def as_dict(self) -> dict[str, Any]:
        return {
            "overall": {
                "pass_at_1": round(self.pass_at_1, 4),
                "pass_at_3": round(self.pass_at_3, 4),
                "pass_pow_3": round(self.pass_pow_3, 4),
            },
            "n_rows": self.n_rows,
            "n_queries": self.n_queries,
            "per_toolkit": self.per_toolkit,
            "retry_report": self.retry_report,
            "notes": self.notes,
        }
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;dict[str, typing.Any]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, pass_at_1, pass_at_3, pass_pow_3, n_rows, n_queries, per_toolkit=dict(), retry_report=dict(), notes=list()) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;pass_at_1&#x22;" type="&#x22;float&#x22;" value="null" />

    <PyParameter name="&#x22;pass_at_3&#x22;" type="&#x22;float&#x22;" value="null" />

    <PyParameter name="&#x22;pass_pow_3&#x22;" type="&#x22;float&#x22;" value="null" />

    <PyParameter name="&#x22;n_rows&#x22;" type="&#x22;int&#x22;" value="null" />

    <PyParameter name="&#x22;n_queries&#x22;" type="&#x22;int&#x22;" value="null" />

    <PyParameter name="&#x22;per_toolkit&#x22;" type="&#x22;dict[str, dict[str, float]]&#x22;" value="&#x22;dict()&#x22;" />

    <PyParameter name="&#x22;retry_report&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;dict()&#x22;" />

    <PyParameter name="&#x22;notes&#x22;" type="&#x22;list[str]&#x22;" value="&#x22;list()&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
