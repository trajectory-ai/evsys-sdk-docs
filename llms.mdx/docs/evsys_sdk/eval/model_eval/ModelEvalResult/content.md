# ModelEvalResult (/docs/evsys_sdk/eval/model_eval/ModelEvalResult)



## Attributes [#attributes]

<PyAttribute name="&#x22;rows&#x22;" type="&#x22;list[dict[str, Any]]&#x22;" value="&#x22;field(default_factory=list)&#x22;">
  Per-row results: \{tool\_slug, toolkit, queries: \[\{query, completion,
  predicted, error}]}.
</PyAttribute>

<PyAttribute name="&#x22;retry_report&#x22;" type="&#x22;RetryReport&#x22;" value="&#x22;field(default_factory=RetryReport)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, rows=list(), retry_report=RetryReport()) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;rows&#x22;" type="&#x22;list[dict[str, Any]]&#x22;" value="&#x22;list()&#x22;" />

    <PyParameter name="&#x22;retry_report&#x22;" type="&#x22;RetryReport&#x22;" value="&#x22;RetryReport()&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
