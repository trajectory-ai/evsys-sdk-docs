# LLMJudgeVerifier (/docs/evsys_sdk/data_types/LLMJudgeVerifier)



LLM judge — `judge_model` scores the completion against `rubric`.

The runner calls the judge model with the rubric + completion and parses a
numeric score from the response (usually in a `\boxed\{\}` or
`\<score>...\</score>` tag).

## Attributes [#attributes]

<PyAttribute name="&#x22;judge_model&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

<PyAttribute name="&#x22;rubric&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

<PyAttribute name="&#x22;kind&#x22;" type="&#x22;Literal['llm_judge']&#x22;" value="&#x22;'llm_judge'&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, judge_model='', rubric='', kind='llm_judge') -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;judge_model&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

    <PyParameter name="&#x22;rubric&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

    <PyParameter name="&#x22;kind&#x22;" type="&#x22;Literal['llm_judge']&#x22;" value="&#x22;'llm_judge'&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
