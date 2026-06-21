# LocalBackendConfig (/docs/evsys_sdk/backends/local/LocalBackendConfig)



## Attributes [#attributes]

<PyAttribute name="&#x22;model_config&#x22;" type="null" value="&#x22;ConfigDict(extra='forbid')&#x22;" />

<PyAttribute name="&#x22;dtype&#x22;" type="&#x22;str&#x22;" value="&#x22;'float32'&#x22;">
  One of: 'bfloat16', 'float16', 'float32'. Defaults to float32 for CPU/MPS compatibility.
</PyAttribute>

<PyAttribute name="&#x22;device&#x22;" type="&#x22;str&#x22;" value="&#x22;'auto'&#x22;">
  'auto' (detect CUDA→MPS→CPU), 'cpu', 'cuda', or 'mps'.
</PyAttribute>

<PyAttribute name="&#x22;trust_remote_code&#x22;" type="&#x22;bool&#x22;" value="&#x22;True&#x22;" />
