# CompletionSlice (/docs/evsys_sdk/training/sdft_data/CompletionSlice)



## Attributes [#attributes]

<PyAttribute name="&#x22;tokens&#x22;" type="&#x22;list[int]&#x22;" value="null">
  The completion tokens themselves.
</PyAttribute>

<PyAttribute name="&#x22;teacher_prompt_len&#x22;" type="&#x22;int&#x22;" value="null">
  Length of the teacher prompt in tokens (used to pull the right slice
  from teacher logprob arrays).
</PyAttribute>

<PyAttribute name="&#x22;truncated&#x22;" type="&#x22;bool&#x22;" value="null" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, tokens, teacher_prompt_len, truncated) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;tokens&#x22;" type="&#x22;list[int]&#x22;" value="null" />

    <PyParameter name="&#x22;teacher_prompt_len&#x22;" type="&#x22;int&#x22;" value="null" />

    <PyParameter name="&#x22;truncated&#x22;" type="&#x22;bool&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
