# ChatTemplatedInference (/docs/evsys_sdk/inference/chat_templated/ChatTemplatedInference)



Wraps an InferenceClient to apply a chat template before each `generate` call.

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'chat_templated'&#x22;" />

<PyAttribute name="&#x22;system_prompt&#x22;" type="null" value="&#x22;system_prompt&#x22;" />

<PyAttribute name="&#x22;user_template&#x22;" type="null" value="&#x22;user_template&#x22;" />

<PyAttribute name="&#x22;enable_thinking&#x22;" type="null" value="&#x22;enable_thinking&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, base, *, system_prompt, user_template='{prompt}', enable_thinking=None) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(
        self,
        base: Any,
        *,
        system_prompt: str,
        user_template: str = "{prompt}",
        enable_thinking: bool | None = None,
    ) -> None:
        if not hasattr(base, "_tokenizer"):
            raise TypeError(
                "base inference client must expose a `_tokenizer` attribute "
                "(HF tokenizer with apply_chat_template); "
                f"got {type(base).__name__}"
            )
        self._base = base
        self.system_prompt = system_prompt
        self.user_template = user_template
        # Forwarded to ``apply_chat_template`` only when set, so non-Qwen
        # tokenizers (which don't accept the kwarg) keep working unchanged.
        self.enable_thinking = enable_thinking
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;base&#x22;" type="&#x22;Any&#x22;" value="null" />

    <PyParameter name="&#x22;system_prompt&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;user_template&#x22;" type="&#x22;str&#x22;" value="&#x22;'{prompt}'&#x22;" />

    <PyParameter name="&#x22;enable_thinking&#x22;" type="&#x22;bool | None&#x22;" value="&#x22;None&#x22;" />
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
        user_content = self.user_template.format(prompt=prompt)
        template_kwargs: dict[str, Any] = {
            "tokenize": False,
            "add_generation_prompt": True,
        }
        if self.enable_thinking is not None:
            template_kwargs["enable_thinking"] = self.enable_thinking
        templated = self._base._tokenizer.apply_chat_template(
            [
                {"role": "system", "content": self.system_prompt},
                {"role": "user", "content": user_content},
            ],
            **template_kwargs,
        )
        return self._base.generate(
            prompt=templated,
            max_tokens=max_tokens,
            temperature=temperature,
            stop=stop,
        )
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
