# DataConfig (/docs/evsys_sdk/config/DataConfig)



Where the training data comes from + how to transform it.

The preferred way to reference a dataset is by `dataset_id` (or
`dataset_name`): the SDK pulls it from the dashboard into the local
`.evsys/` workspace and trains from that cache — so stored
experiment scripts are portable and don't depend on local file layout.
`dataset_id` / `dataset_name` take precedence over `source_kind` /
`path`, which remain as an offline / dev fallback.

## Attributes [#attributes]

<PyAttribute name="&#x22;source_kind&#x22;" type="&#x22;Literal['jsonl', 'json', 'in_memory', 'hf_dataset']&#x22;" value="&#x22;'jsonl'&#x22;">
  Which built-in source loader to use (ignored when dataset\_id/name set).
</PyAttribute>

<PyAttribute name="&#x22;dataset_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  Dashboard dataset id — pulled into .evsys/ and trained from locally.
</PyAttribute>

<PyAttribute name="&#x22;dataset_name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  Dashboard dataset name — resolved to the latest version's id, then pulled.
</PyAttribute>

<PyAttribute name="&#x22;path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  For 'jsonl' / 'json': absolute or store-relative path.
</PyAttribute>

<PyAttribute name="&#x22;rows&#x22;" type="&#x22;list[dict[str, Any]] | None&#x22;" value="&#x22;None&#x22;">
  For 'in\_memory'.
</PyAttribute>

<PyAttribute name="&#x22;hf_dataset&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  For 'hf\_dataset': HuggingFace dataset id.
</PyAttribute>

<PyAttribute name="&#x22;hf_split&#x22;" type="&#x22;str&#x22;" value="&#x22;'train'&#x22;" />

<PyAttribute name="&#x22;transforms&#x22;" type="&#x22;list[TransformSpec]&#x22;" value="&#x22;Field(default_factory=list)&#x22;">
  Applied in order to the raw rows.
</PyAttribute>
