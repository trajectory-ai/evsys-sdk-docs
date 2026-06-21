# LocalBackend (/docs/evsys_sdk/backends/local/LocalBackend)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'local'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;LocalBackendConfig&#x22;" />

<PyAttribute name="&#x22;dtype&#x22;" type="null" value="&#x22;dtype&#x22;" />

<PyAttribute name="&#x22;device&#x22;" type="null" value="&#x22;device&#x22;" />

<PyAttribute name="&#x22;trust_remote_code&#x22;" type="null" value="&#x22;trust_remote_code&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, dtype='float32', device='auto', trust_remote_code=True) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(
        self,
        *,
        dtype: str = "float32",
        device: str = "auto",
        trust_remote_code: bool = True,
    ) -> None:
        self.dtype = dtype
        self.device = device
        self.trust_remote_code = trust_remote_code
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;dtype&#x22;" type="&#x22;str&#x22;" value="&#x22;'float32'&#x22;" />

    <PyParameter name="&#x22;device&#x22;" type="&#x22;str&#x22;" value="&#x22;'auto'&#x22;" />

    <PyParameter name="&#x22;trust_remote_code&#x22;" type="&#x22;bool&#x22;" value="&#x22;True&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_resolve_device&#x22;" type="&#x22;(self) -> str&#x22;">
  <PySourceCode>
    ```python
    def _resolve_device(self) -> str:
        if self.device != "auto":
            return self.device
        if torch.cuda.is_available():
            return "cuda"
        if torch.backends.mps.is_available():
            return "mps"
        return "cpu"
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str&#x22;" />
</PyFunction>

<PyFunction name="&#x22;prepare&#x22;" type="&#x22;(self, *, model, run_dir) -> dict[str, Any]&#x22;">
  <PySourceCode>
    ```python
    def prepare(self, *, model: dict[str, Any], run_dir: str) -> dict[str, Any]:
        # Lazy imports to keep cold-start fast.
        from transformers import AutoModelForCausalLM, AutoTokenizer

        torch_dtype = _DTYPE_MAP.get(self.dtype, torch.float32)
        device = self._resolve_device()

        tokenizer = AutoTokenizer.from_pretrained(
            model["name"], trust_remote_code=self.trust_remote_code
        )
        if tokenizer.pad_token is None:
            tokenizer.pad_token = tokenizer.eos_token

        # MPS does not support device_map="auto"; load to CPU then move.
        if device == "mps":
            hf_model = AutoModelForCausalLM.from_pretrained(
                model["name"],
                torch_dtype=torch_dtype,
                trust_remote_code=self.trust_remote_code,
            ).to("mps")
        else:
            hf_model = AutoModelForCausalLM.from_pretrained(
                model["name"],
                torch_dtype=torch_dtype,
                trust_remote_code=self.trust_remote_code,
                device_map=device,
            )

        return {
            "backend": "local",
            "model": hf_model,
            "tokenizer": tokenizer,
            "model_name": model["name"],
            "device": device,
            "run_dir": run_dir,
        }
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;model&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />

    <PyParameter name="&#x22;run_dir&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;dict[str, typing.Any]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;teardown&#x22;" type="&#x22;(self, handles) -> None&#x22;">
  <PySourceCode>
    ```python
    def teardown(self, handles: dict[str, Any]) -> None:
        handles.pop("model", None)
        if torch.cuda.is_available():
            torch.cuda.empty_cache()
        elif torch.backends.mps.is_available():
            torch.mps.empty_cache()
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;handles&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
