# LocalInference (/docs/evsys_sdk/inference/local/LocalInference)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'local'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;LocalInferenceConfig&#x22;" />

<PyAttribute name="&#x22;tokenizer&#x22;" type="null" value="&#x22;AutoTokenizer.from_pretrained(model_name, trust_remote_code=True)&#x22;" />

<PyAttribute name="&#x22;model&#x22;" type="null" value="&#x22;AutoModelForCausalLM.from_pretrained(model_name, dtype=torch_dtype, trust_remote_code=True, device_map=(device if device != 'auto' else 'auto'))&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, model_name, adapter_path=None, dtype='bfloat16', device='auto') -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(
        self,
        *,
        model_name: str,
        adapter_path: str | None = None,
        dtype: str = "bfloat16",
        device: str = "auto",
    ) -> None:
        from transformers import AutoModelForCausalLM, AutoTokenizer

        self.tokenizer = AutoTokenizer.from_pretrained(model_name, trust_remote_code=True)
        if self.tokenizer.pad_token is None:
            self.tokenizer.pad_token = self.tokenizer.eos_token
        torch_dtype = _DTYPE_MAP.get(dtype, torch.float32)
        self.model = AutoModelForCausalLM.from_pretrained(
            model_name,
            dtype=torch_dtype,
            trust_remote_code=True,
            device_map=device if device != "auto" else "auto",
        )
        if adapter_path:
            from peft import PeftModel

            self.model = PeftModel.from_pretrained(self.model, adapter_path)
        self.model.eval()
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;model_name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;adapter_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;dtype&#x22;" type="&#x22;str&#x22;" value="&#x22;'bfloat16'&#x22;" />

    <PyParameter name="&#x22;device&#x22;" type="&#x22;str&#x22;" value="&#x22;'auto'&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;generate&#x22;" type="&#x22;(self, *, prompt, max_tokens=256, temperature=0.0, stop=None) -> str&#x22;">
  <PySourceCode>
    ```python
    def generate(
        self,
        *,
        prompt: str,
        max_tokens: int = 256,
        temperature: float = 0.0,
        stop: list[str] | None = None,
    ) -> str:
        inputs = self.tokenizer(prompt, return_tensors="pt", truncation=True, max_length=4096)
        inputs = {k: v.to(self.model.device) for k, v in inputs.items()}
        do_sample = temperature > 0.0
        with torch.no_grad():
            out = self.model.generate(
                **inputs,
                max_new_tokens=max_tokens,
                temperature=temperature if do_sample else 1.0,
                do_sample=do_sample,
                pad_token_id=self.tokenizer.pad_token_id,
            )
        decoded = self.tokenizer.decode(
            out[0][inputs["input_ids"].shape[1]:], skip_special_tokens=True
        )
        if stop:
            for s in stop:
                idx = decoded.find(s)
                if idx >= 0:
                    decoded = decoded[:idx]
        return decoded
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;prompt&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;max_tokens&#x22;" type="&#x22;int&#x22;" value="&#x22;256&#x22;" />

    <PyParameter name="&#x22;temperature&#x22;" type="&#x22;float&#x22;" value="&#x22;0.0&#x22;" />

    <PyParameter name="&#x22;stop&#x22;" type="&#x22;list[str] | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;str&#x22;" />
</PyFunction>
