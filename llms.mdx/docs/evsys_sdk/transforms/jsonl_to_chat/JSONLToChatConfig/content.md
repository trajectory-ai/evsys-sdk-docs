# JSONLToChatConfig (/docs/evsys_sdk/transforms/jsonl_to_chat/JSONLToChatConfig)



## Attributes [#attributes]

<PyAttribute name="&#x22;model_config&#x22;" type="null" value="&#x22;ConfigDict(extra='forbid')&#x22;" />

<PyAttribute name="&#x22;system_prompt&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

<PyAttribute name="&#x22;user_template&#x22;" type="&#x22;str&#x22;" value="&#x22;'{query}'&#x22;">
  Format string with \{field} placeholders pulled from each row.
</PyAttribute>

<PyAttribute name="&#x22;assistant_template&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  If set, builds an assistant message too (for SFT). e.g. '\{answer}'.
</PyAttribute>
