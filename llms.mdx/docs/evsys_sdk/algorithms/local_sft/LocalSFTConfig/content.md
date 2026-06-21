# LocalSFTConfig (/docs/evsys_sdk/algorithms/local_sft/LocalSFTConfig)



## Attributes [#attributes]

<PyAttribute name="&#x22;model_config&#x22;" type="null" value="&#x22;ConfigDict(extra='forbid')&#x22;" />

<PyAttribute name="&#x22;learning_rate&#x22;" type="&#x22;float&#x22;" value="&#x22;0.0002&#x22;" />

<PyAttribute name="&#x22;num_epochs&#x22;" type="&#x22;int&#x22;" value="&#x22;3&#x22;" />

<PyAttribute name="&#x22;max_steps&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;">
  If set, overrides num\_epochs — training stops after this many steps.
</PyAttribute>

<PyAttribute name="&#x22;per_device_train_batch_size&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />

<PyAttribute name="&#x22;gradient_accumulation_steps&#x22;" type="&#x22;int&#x22;" value="&#x22;16&#x22;" />

<PyAttribute name="&#x22;warmup_steps&#x22;" type="&#x22;int&#x22;" value="&#x22;20&#x22;" />

<PyAttribute name="&#x22;max_seq_len&#x22;" type="&#x22;int&#x22;" value="&#x22;512&#x22;" />

<PyAttribute name="&#x22;logging_steps&#x22;" type="&#x22;int&#x22;" value="&#x22;10&#x22;" />

<PyAttribute name="&#x22;save_steps&#x22;" type="&#x22;int&#x22;" value="&#x22;100&#x22;" />

<PyAttribute name="&#x22;save_total_limit&#x22;" type="&#x22;int&#x22;" value="&#x22;5&#x22;" />

<PyAttribute name="&#x22;bf16&#x22;" type="&#x22;bool&#x22;" value="&#x22;False&#x22;">
  Enable only on CUDA GPUs that support bfloat16 (Ampere+). Crashes on CPU/MPS.
</PyAttribute>

<PyAttribute name="&#x22;fp16&#x22;" type="&#x22;bool&#x22;" value="&#x22;False&#x22;">
  Enable only on CUDA GPUs. Crashes on CPU/MPS.
</PyAttribute>

<PyAttribute name="&#x22;seed&#x22;" type="&#x22;int&#x22;" value="&#x22;42&#x22;" />

<PyAttribute name="&#x22;lora_rank&#x22;" type="&#x22;int&#x22;" value="&#x22;16&#x22;" />

<PyAttribute name="&#x22;lora_alpha&#x22;" type="&#x22;int&#x22;" value="&#x22;32&#x22;" />

<PyAttribute name="&#x22;lora_dropout&#x22;" type="&#x22;float&#x22;" value="&#x22;0.05&#x22;" />

<PyAttribute name="&#x22;lora_target_modules&#x22;" type="&#x22;list[str]&#x22;" value="&#x22;Field(default_factory=(lambda: ['q_proj', 'k_proj', 'v_proj', 'o_proj', 'gate_proj', 'up_proj', 'down_proj']))&#x22;" />
