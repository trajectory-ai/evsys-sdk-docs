# SDFTConfig (/docs/evsys_sdk/algorithms/sdft/SDFTConfig)



Config for :class:`SDFT`. Inherits the shared training/save/eval knobs
from :class:`BaseAlgorithmConfig`; adds SDFT-only fields.

## Attributes [#attributes]

<PyAttribute name="&#x22;topk&#x22;" type="&#x22;int&#x22;" value="&#x22;20&#x22;" />

<PyAttribute name="&#x22;teacher_sync_every&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;max_context_length&#x22;" type="&#x22;int&#x22;" value="&#x22;2048&#x22;" />

<PyAttribute name="&#x22;demo_template&#x22;" type="&#x22;str&#x22;" value="&#x22;DEFAULT_DEMO_TEMPLATE&#x22;" />

<PyAttribute name="&#x22;system_prompt&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;skip_first_n_tokens&#x22;" type="&#x22;int&#x22;" value="&#x22;3&#x22;" />

<PyAttribute name="&#x22;user_template&#x22;" type="&#x22;str&#x22;" value="&#x22;'{question}'&#x22;" />

<PyAttribute name="&#x22;max_tokens&#x22;" type="&#x22;int&#x22;" value="&#x22;256&#x22;" />

<PyAttribute name="&#x22;temperature&#x22;" type="&#x22;float&#x22;" value="&#x22;1.0&#x22;" />
