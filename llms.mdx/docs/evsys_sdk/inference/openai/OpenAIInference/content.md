# OpenAIInference (/docs/evsys_sdk/inference/openai/OpenAIInference)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'openai'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;OpenAIInferenceConfig&#x22;" />

<PyAttribute name="&#x22;cfg&#x22;" type="null" value="&#x22;OpenAIInferenceConfig.model_validate(kwargs)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, **kwargs) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, **kwargs) -> None:
        try:
            from openai import OpenAI  # type: ignore[import-not-found]
        except ImportError as e:
            raise ImportError(
                "OpenAIInference requires the `openai` package. "
                "Install with: pip install openai"
            ) from e

        self.cfg = OpenAIInferenceConfig.model_validate(kwargs)
        key = os.environ.get(self.cfg.api_key_env)
        if not key:
            raise RuntimeError(
                f"OpenAIInference: env var {self.cfg.api_key_env} is not set"
            )
        ctor: dict = {"api_key": key, "timeout": self.cfg.timeout_s}
        if self.cfg.base_url:     ctor["base_url"]     = self.cfg.base_url
        if self.cfg.organization: ctor["organization"] = self.cfg.organization
        self._client = OpenAI(**ctor)
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
        messages: list[dict] = []
        if self.cfg.system_prompt:
            messages.append({"role": "system", "content": self.cfg.system_prompt})
        messages.append({"role": "user", "content": prompt})

        kwargs: dict = {
            "model": self.cfg.model,
            "messages": messages,
            "max_tokens": max_tokens or self.cfg.default_max_tokens,
            "temperature": temperature if temperature is not None else self.cfg.default_temperature,
        }
        if stop:
            kwargs["stop"] = stop

        resp = self._client.chat.completions.create(**kwargs)
        choices = getattr(resp, "choices", []) or []
        if not choices:
            return ""
        msg = choices[0].message
        return getattr(msg, "content", None) or ""
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
