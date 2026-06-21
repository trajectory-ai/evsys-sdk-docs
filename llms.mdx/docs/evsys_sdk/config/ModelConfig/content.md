# ModelConfig (/docs/evsys_sdk/config/ModelConfig)



Which model to fine-tune.

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null">
  HuggingFace identifier, e.g. 'Qwen/Qwen3-4B'.
</PyAttribute>

<PyAttribute name="&#x22;load_checkpoint_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  Resume from a previous checkpoint *with* optimizer state (full resume).
</PyAttribute>

<PyAttribute name="&#x22;init_from_checkpoint&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  Initialise weights from a previous checkpoint but start a *fresh*
  optimizer (weights-only). Used to chain continual-learning stages.
</PyAttribute>

<PyAttribute name="&#x22;renderer_name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  Tinker chat renderer hint, e.g. 'qwen3'.
</PyAttribute>
