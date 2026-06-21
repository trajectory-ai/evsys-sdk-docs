# GeminiInference (/docs/evsys_sdk/inference/gemini/GeminiInference)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'gemini'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;GeminiInferenceConfig&#x22;" />

<PyAttribute name="&#x22;cfg&#x22;" type="null" value="&#x22;GeminiInferenceConfig.model_validate(kwargs)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, **kwargs) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, **kwargs) -> None:
        try:
            from google import genai  # type: ignore[import-not-found]
            from google.genai import types as genai_types  # type: ignore[import-not-found]
        except ImportError as e:
            raise ImportError(
                "GeminiInference requires the `google-genai` package. "
                "Install with: pip install google-genai"
            ) from e

        self.cfg = GeminiInferenceConfig.model_validate(kwargs)
        self._types = genai_types
        key = os.environ.get(self.cfg.api_key_env) or os.environ.get(self.cfg.fallback_api_key_env)
        if not key:
            raise RuntimeError(
                f"GeminiInference: env var {self.cfg.api_key_env} (or "
                f"{self.cfg.fallback_api_key_env}) is not set"
            )
        self._client = genai.Client(api_key=key)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;kwargs&#x22;" type="null" value="&#x22;{}&#x22;" />
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
        gen_config = self._types.GenerateContentConfig(
            max_output_tokens=max_tokens or self.cfg.default_max_tokens,
            temperature=temperature if temperature is not None else self.cfg.default_temperature,
            stop_sequences=stop or None,
            system_instruction=self.cfg.system_instruction,
        )
        resp = self._client.models.generate_content(
            model=self.cfg.model,
            contents=prompt,
            config=gen_config,
        )
        return getattr(resp, "text", None) or ""
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
