# ClaudeInference (/docs/evsys_sdk/inference/claude/ClaudeInference)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'claude'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;ClaudeInferenceConfig&#x22;" />

<PyAttribute name="&#x22;cfg&#x22;" type="null" value="&#x22;ClaudeInferenceConfig.model_validate(kwargs)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, **kwargs) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, **kwargs) -> None:
        try:
            from anthropic import Anthropic  # type: ignore[import-not-found]
        except ImportError as e:
            raise ImportError(
                "ClaudeInference requires the `anthropic` package. "
                "Install with: pip install anthropic"
            ) from e

        self.cfg = ClaudeInferenceConfig.model_validate(kwargs)
        key = os.environ.get(self.cfg.api_key_env)
        if not key:
            raise RuntimeError(
                f"ClaudeInference: env var {self.cfg.api_key_env} is not set"
            )
        ctor_kwargs: dict = {"api_key": key, "timeout": self.cfg.timeout_s}
        if self.cfg.base_url:
            ctor_kwargs["base_url"] = self.cfg.base_url
        self._client = Anthropic(**ctor_kwargs)
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
        kwargs: dict = {
            "model": self.cfg.model,
            "max_tokens": max_tokens or self.cfg.default_max_tokens,
            "temperature": temperature if temperature is not None else self.cfg.default_temperature,
            "messages": [{"role": "user", "content": prompt}],
        }
        if self.cfg.system_prompt:
            kwargs["system"] = self.cfg.system_prompt
        if stop:
            kwargs["stop_sequences"] = stop
        if self.cfg.extra_headers:
            kwargs["extra_headers"] = self.cfg.extra_headers

        resp = self._client.messages.create(**kwargs)
        # Anthropic returns a list of content blocks; we concatenate the text ones.
        parts: list[str] = []
        for block in (resp.content or []):
            if getattr(block, "type", "") == "text":
                parts.append(getattr(block, "text", ""))
        return "".join(parts)
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
