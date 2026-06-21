# GEPAPromptConfig (/docs/evsys_sdk/algorithms/gepa_prompt/GEPAPromptConfig)



## Attributes [#attributes]

<PyAttribute name="&#x22;model_config&#x22;" type="null" value="&#x22;ConfigDict(extra='forbid')&#x22;" />

<PyAttribute name="&#x22;seed_prompt&#x22;" type="&#x22;str&#x22;" value="&#x22;'You are a helpful assistant. Answer concisely.'&#x22;">
  Initial system prompt.
</PyAttribute>

<PyAttribute name="&#x22;task_lm&#x22;" type="&#x22;str&#x22;" value="&#x22;'mock'&#x22;">
  Registry name of the inference client used to evaluate each candidate.
</PyAttribute>

<PyAttribute name="&#x22;task_lm_config&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;Field(default_factory=dict)&#x22;" />

<PyAttribute name="&#x22;reflection_lm&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  Optional inference client used to propose mutated prompts; required for
  the real gepa path. Without it we fall back to the built-in mutator.
</PyAttribute>

<PyAttribute name="&#x22;reflection_lm_config&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;Field(default_factory=dict)&#x22;" />

<PyAttribute name="&#x22;max_iterations&#x22;" type="&#x22;int&#x22;" value="&#x22;20&#x22;" />

<PyAttribute name="&#x22;population_size&#x22;" type="&#x22;int&#x22;" value="&#x22;4&#x22;" />

<PyAttribute name="&#x22;examples_per_eval&#x22;" type="&#x22;int&#x22;" value="&#x22;8&#x22;">
  How many PromptExamples to evaluate each candidate on (per iteration).
</PyAttribute>

<PyAttribute name="&#x22;use_gepa_lib&#x22;" type="&#x22;bool&#x22;" value="&#x22;True&#x22;">
  If True and the `gepa` package is importable, delegate to it.
</PyAttribute>

<PyAttribute name="&#x22;seed&#x22;" type="&#x22;int&#x22;" value="&#x22;1729&#x22;" />
