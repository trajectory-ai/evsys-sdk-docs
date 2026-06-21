# ClaudeInferenceConfig (/docs/evsys_sdk/inference/claude/ClaudeInferenceConfig)



## Attributes [#attributes]

<PyAttribute name="&#x22;model_config&#x22;" type="null" value="&#x22;ConfigDict(extra='forbid')&#x22;" />

<PyAttribute name="&#x22;model&#x22;" type="&#x22;str&#x22;" value="&#x22;'claude-sonnet-4-6'&#x22;" />

<PyAttribute name="&#x22;api_key_env&#x22;" type="&#x22;str&#x22;" value="&#x22;'ANTHROPIC_API_KEY'&#x22;" />

<PyAttribute name="&#x22;base_url&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;default_max_tokens&#x22;" type="&#x22;int&#x22;" value="&#x22;1024&#x22;" />

<PyAttribute name="&#x22;default_temperature&#x22;" type="&#x22;float&#x22;" value="&#x22;0.0&#x22;" />

<PyAttribute name="&#x22;system_prompt&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  Optional system prompt prepended to every call. Per-call `prompt` is
  sent as the single user message.
</PyAttribute>

<PyAttribute name="&#x22;timeout_s&#x22;" type="&#x22;float&#x22;" value="&#x22;60.0&#x22;" />

<PyAttribute name="&#x22;extra_headers&#x22;" type="&#x22;dict[str, str]&#x22;" value="&#x22;Field(default_factory=dict)&#x22;" />
