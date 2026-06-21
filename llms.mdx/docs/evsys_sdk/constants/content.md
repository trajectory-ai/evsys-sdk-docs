# constants (/docs/evsys_sdk/constants)



Central constants for the evsys\_sdk SDK.

Single source of truth for env-var names, defaults, HTTP endpoint paths,
status strings, and logging config. Change endpoints / defaults here — not
scattered across the codebase.

Distinct from `config.py`, which holds the user-facing Pydantic *experiment*
config models (ExperimentConfig, RunConfig, ...). This module is SDK app-level
plumbing.

<PyAttribute name="&#x22;EVSYS_API_URL_ENV&#x22;" type="null" value="&#x22;'EVSYS_API_URL'&#x22;" />

<PyAttribute name="&#x22;EVSYS_API_KEY_ENV&#x22;" type="null" value="&#x22;'EVSYS_API_KEY'&#x22;" />

<PyAttribute name="&#x22;EVSYS_PROJECT_ID_ENV&#x22;" type="null" value="&#x22;'EVSYS_PROJECT_ID'&#x22;" />

<PyAttribute name="&#x22;EVSYS_LOG_DIR_ENV&#x22;" type="null" value="&#x22;'EVSYS_LOG_DIR'&#x22;" />

<PyAttribute name="&#x22;EVSYS_OFFLINE_ENV&#x22;" type="null" value="&#x22;'EVSYS_OFFLINE'&#x22;" />

<PyAttribute name="&#x22;EVSYS_LOGGING_LEVEL_ENV&#x22;" type="null" value="&#x22;'EVSYS_LOGGING_LEVEL'&#x22;" />

<PyAttribute name="&#x22;DEFAULT_API_URL&#x22;" type="null" value="&#x22;'http://localhost:8000'&#x22;" />

<PyAttribute name="&#x22;DEFAULT_LOG_DIR&#x22;" type="null" value="&#x22;'./evsys_sdk'&#x22;" />

<PyAttribute name="&#x22;DEFAULT_TIMEOUT_S&#x22;" type="null" value="&#x22;30.0&#x22;" />

<PyAttribute name="&#x22;API_PREFIX&#x22;" type="null" value="&#x22;'/api/dashboard/api'&#x22;" />

<PyAttribute name="&#x22;EP_CREATE_EXPERIMENT&#x22;" type="null" value="&#x22;'/sdk/experiments/'&#x22;" />

<PyAttribute name="&#x22;EP_UPDATE_EXPERIMENT&#x22;" type="null" value="&#x22;'/sdk/experiments/{experiment_id}/'&#x22;" />

<PyAttribute name="&#x22;EP_CREATE_RUN&#x22;" type="null" value="&#x22;'/sdk/runs/'&#x22;" />

<PyAttribute name="&#x22;EP_UPDATE_RUN&#x22;" type="null" value="&#x22;'/sdk/runs/{run_id}/'&#x22;" />

<PyAttribute name="&#x22;EP_LOG_METRICS&#x22;" type="null" value="&#x22;'/sdk/runs/{run_id}/metrics/'&#x22;" />

<PyAttribute name="&#x22;EP_LOG_EVAL&#x22;" type="null" value="&#x22;'/sdk/runs/{run_id}/evals/'&#x22;" />

<PyAttribute name="&#x22;EP_ADD_CHECKPOINT&#x22;" type="null" value="&#x22;'/sdk/runs/{run_id}/checkpoints/'&#x22;" />

<PyAttribute name="&#x22;EP_LOG_PREDICTIONS&#x22;" type="null" value="&#x22;'/sdk/runs/{run_id}/predictions/'&#x22;" />

<PyAttribute name="&#x22;KEY_EXPERIMENT&#x22;" type="null" value="&#x22;'experiment'&#x22;" />

<PyAttribute name="&#x22;KEY_RUN&#x22;" type="null" value="&#x22;'run'&#x22;" />

<PyAttribute name="&#x22;KEY_RAW&#x22;" type="null" value="&#x22;'_raw'&#x22;" />

<PyAttribute name="&#x22;FIELD_PROJECT_ID&#x22;" type="null" value="&#x22;'project_id'&#x22;" />

<PyAttribute name="&#x22;FIELD_BEST_SCORE&#x22;" type="null" value="&#x22;'best_score'&#x22;" />

<PyAttribute name="&#x22;FIELD_ERROR_MESSAGE&#x22;" type="null" value="&#x22;'error_message'&#x22;" />

<PyAttribute name="&#x22;FIELD_CONCLUSION&#x22;" type="null" value="&#x22;'conclusion'&#x22;" />

<PyAttribute name="&#x22;STATUS_PENDING&#x22;" type="null" value="&#x22;'pending'&#x22;" />

<PyAttribute name="&#x22;STATUS_RUNNING&#x22;" type="null" value="&#x22;'running'&#x22;" />

<PyAttribute name="&#x22;STATUS_COMPLETED&#x22;" type="null" value="&#x22;'completed'&#x22;" />

<PyAttribute name="&#x22;STATUS_FAILED&#x22;" type="null" value="&#x22;'failed'&#x22;" />

<PyAttribute name="&#x22;STATUS_CANCELLED&#x22;" type="null" value="&#x22;'cancelled'&#x22;" />

<PyAttribute name="&#x22;HEADER_AUTHORIZATION&#x22;" type="null" value="&#x22;'Authorization'&#x22;" />

<PyAttribute name="&#x22;HEADER_CONTENT_TYPE&#x22;" type="null" value="&#x22;'Content-Type'&#x22;" />

<PyAttribute name="&#x22;CONTENT_TYPE_JSON&#x22;" type="null" value="&#x22;'application/json'&#x22;" />

<PyAttribute name="&#x22;DEFAULT_LOGGING_LEVEL&#x22;" type="null" value="&#x22;'INFO'&#x22;" />

<PyAttribute name="&#x22;SUPPORTED_LOGGING_LEVELS&#x22;" type="null" value="&#x22;['DEBUG', 'INFO', 'WARNING', 'ERROR', 'CRITICAL']&#x22;" />

<PyAttribute name="&#x22;DEFAULT_LOG_FORMAT&#x22;" type="null" value="&#x22;'%(asctime)s - %(name)s - %(levelname)s - %(filename)s:%(lineno)d - %(message)s'&#x22;" />

<PyAttribute name="&#x22;DEFAULT_LOG_DATE_FORMAT&#x22;" type="null" value="&#x22;'%Y-%m-%d %H:%M:%S'&#x22;" />

<PyAttribute name="&#x22;LOGGER_NAME&#x22;" type="null" value="&#x22;'evsys_sdk'&#x22;" />

<PyAttribute name="&#x22;LOCAL_EXPERIMENT_FILE&#x22;" type="null" value="&#x22;'experiment.json'&#x22;" />

<PyAttribute name="&#x22;LOCAL_GENERATION_FILE&#x22;" type="null" value="&#x22;'generation.json'&#x22;" />

<PyAttribute name="&#x22;LOCAL_METRICS_FILE&#x22;" type="null" value="&#x22;'metrics.jsonl'&#x22;" />

<PyAttribute name="&#x22;LOCAL_EVALS_FILE&#x22;" type="null" value="&#x22;'evals.jsonl'&#x22;" />

<PyAttribute name="&#x22;LOCAL_PREDICTIONS_FILE&#x22;" type="null" value="&#x22;'predictions.jsonl'&#x22;" />

<Tabs items="[&#x22;Functions&#x22;]">
  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;bearer&#x22;" type="&#x22;(api_key) -> str&#x22;">
      <PySourceCode>
        ```python
        def bearer(api_key: str) -> str:
            return f"Bearer {api_key}"
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;api_key&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;str&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;truthy_env&#x22;" type="&#x22;(value) -> bool&#x22;">
      Interpret an env var string as a boolean.

      <PySourceCode>
        ```python
        def truthy_env(value: str | None) -> bool:
            """Interpret an env var string as a boolean."""
            return (value or "").strip().lower() in {"1", "true", "yes", "on"}
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;value&#x22;" type="&#x22;str | None&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;bool&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
