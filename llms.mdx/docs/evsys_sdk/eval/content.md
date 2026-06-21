# eval (/docs/evsys_sdk/eval)



Generic eval infra for tool-detection / model-output scoring.

Public surface:

from evsys\_sdk.eval import (
AliasMatcher, ModelEvalConfig,
evaluate\_model, EvalArtifacts, EvalSummary,
RetryReport, call\_with\_retry,
score\_rows, format\_summary\_markdown,
)

Every inference call is wrapped in `call_with_retry` (configurable attempts +
backoff); exhausted failures surface via `RetryReport` instead of aborting.
This module is domain-agnostic — project-specific eval harnesses (e.g. an API
search eval) build on this infra in their own repos.

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['AliasMatcher', 'EvalArtifacts', 'EvalSummary', 'ModelEvalConfig', 'ModelEvalResult', 'RetryFailure', 'RetryReport', 'call_with_retry', 'evaluate_model', 'extract_predicted_slug', 'format_summary_markdown', 'load_eval_dataset', 'model_query_found', 'qwen_chat_prompt', 'run_model_eval', 'score_rows']&#x22;" />

<Tabs items="[&#x22;Modules&#x22;]">
  <Tab value="&#x22;Modules&#x22;">
    <Cards>
      <Card href="&#x22;/docs/evsys_sdk/eval/runner&#x22;" title="&#x22;runner&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/eval/matcher&#x22;" title="&#x22;matcher&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/eval/retry&#x22;" title="&#x22;retry&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/eval/model_eval&#x22;" title="&#x22;model_eval&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/eval/report&#x22;" title="&#x22;report&#x22;" />
    </Cards>
  </Tab>
</Tabs>
