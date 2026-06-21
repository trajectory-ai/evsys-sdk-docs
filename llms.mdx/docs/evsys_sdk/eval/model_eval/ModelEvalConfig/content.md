# ModelEvalConfig (/docs/evsys_sdk/eval/model_eval/ModelEvalConfig)



## Attributes [#attributes]

<PyAttribute name="&#x22;max_tokens&#x22;" type="&#x22;int&#x22;" value="&#x22;256&#x22;" />

<PyAttribute name="&#x22;temperature&#x22;" type="&#x22;float&#x22;" value="&#x22;0.0&#x22;" />

<PyAttribute name="&#x22;max_attempts&#x22;" type="&#x22;int&#x22;" value="&#x22;5&#x22;">
  Retries per generation call (transient inference errors).
</PyAttribute>

<PyAttribute name="&#x22;batch_size&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;">
  When >1 and the client exposes `generate_batch`, submit prompts in
  chunks of this size and collect them concurrently. Falls back to
  sequential `generate` calls if the client doesn't support batching.
</PyAttribute>

<PyAttribute name="&#x22;prompt_builder&#x22;" type="&#x22;PromptBuilder&#x22;" value="&#x22;qwen_chat_prompt&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, max_tokens=256, temperature=0.0, max_attempts=5, batch_size=1, prompt_builder=qwen_chat_prompt) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;max_tokens&#x22;" type="&#x22;int&#x22;" value="&#x22;256&#x22;" />

    <PyParameter name="&#x22;temperature&#x22;" type="&#x22;float&#x22;" value="&#x22;0.0&#x22;" />

    <PyParameter name="&#x22;max_attempts&#x22;" type="&#x22;int&#x22;" value="&#x22;5&#x22;" />

    <PyParameter name="&#x22;batch_size&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />

    <PyParameter name="&#x22;prompt_builder&#x22;" type="&#x22;PromptBuilder&#x22;" value="&#x22;qwen_chat_prompt&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
