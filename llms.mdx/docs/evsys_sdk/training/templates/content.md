# templates (/docs/evsys_sdk/training/templates)



Chat-template helpers — thin wrappers over `tokenizer.apply_chat_template`.

We deliberately do NOT reproduce tinker\_cookbook's `Renderer` class hierarchy
— Hugging Face's tokenizers already implement the template via
`apply_chat_template`, and the cookbook's `Renderer` subclasses just wrap
that call with per-model defaults (`enable_thinking=False` for Qwen3.5-disable
variants, etc.). We forward the kwarg directly when set, which keeps every
HF chat-template-aware tokenizer working uniformly.

The functions here return `tinker.ModelInput` so they're drop-in for any
sampling / forward call.

<PyAttribute name="&#x22;Message&#x22;" type="null" value="&#x22;dict[str, Any]&#x22;">
  Role-tagged chat message — `\{"role": "system" | "user" | "assistant", "content": "..."\}`.
  Matches the HF `apply_chat_template` input shape.
</PyAttribute>

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['Message', 'apply_template', 'messages_to_model_input', 'text_to_model_input']&#x22;" />

<Tabs items="[&#x22;Functions&#x22;]">
  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;apply_template&#x22;" type="&#x22;(tokenizer, messages, *, add_generation_prompt, enable_thinking=None) -> str&#x22;">
      Call `apply_chat_template` with `enable_thinking` forwarded only
      when explicitly set.

      Why the gate: Qwen3.5 tokenizers accept `enable_thinking`, but most
      others do NOT and raise on the unknown kwarg. Forwarding only when set
      keeps the helper tokenizer-agnostic — same approach already in
      `ChatTemplatedInference` (`inference/chat_templated.py`).

      <PySourceCode>
        ```python
        def apply_template(
            tokenizer: Any,
            messages: Sequence[Message],
            *,
            add_generation_prompt: bool,
            enable_thinking: bool | None = None,
        ) -> str:
            """Call ``apply_chat_template`` with ``enable_thinking`` forwarded only
            when explicitly set.

            Why the gate: Qwen3.5 tokenizers accept ``enable_thinking``, but most
            others do NOT and raise on the unknown kwarg. Forwarding only when set
            keeps the helper tokenizer-agnostic — same approach already in
            ``ChatTemplatedInference`` (``inference/chat_templated.py``).
            """
            kwargs: dict[str, Any] = {
                "tokenize": False,
                "add_generation_prompt": add_generation_prompt,
            }
            if enable_thinking is not None:
                kwargs["enable_thinking"] = enable_thinking
            return tokenizer.apply_chat_template(list(messages), **kwargs)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;tokenizer&#x22;" type="&#x22;Any&#x22;" value="null" />

        <PyParameter name="&#x22;messages&#x22;" type="&#x22;Sequence[Message]&#x22;" value="null" />

        <PyParameter name="&#x22;add_generation_prompt&#x22;" type="&#x22;bool&#x22;" value="null" />

        <PyParameter name="&#x22;enable_thinking&#x22;" type="&#x22;bool | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="&#x22;str&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;messages_to_model_input&#x22;" type="&#x22;(tokenizer, messages, *, add_generation_prompt=True, enable_thinking=None) -> tinker.ModelInput&#x22;">
      Apply chat template → encode → build `tinker.ModelInput`.

      Use `add_generation_prompt=True` for sampling prompts (the assistant
      turn isn't included yet; the model writes it). `False` when building
      a completed sequence (e.g. SFT teacher-forcing).

      <PySourceCode>
        ```python
        def messages_to_model_input(
            tokenizer: Any,
            messages: Sequence[Message],
            *,
            add_generation_prompt: bool = True,
            enable_thinking: bool | None = None,
        ) -> tinker.ModelInput:
            """Apply chat template → encode → build ``tinker.ModelInput``.

            Use ``add_generation_prompt=True`` for sampling prompts (the assistant
            turn isn't included yet; the model writes it). ``False`` when building
            a completed sequence (e.g. SFT teacher-forcing).
            """
            text = apply_template(
                tokenizer, messages,
                add_generation_prompt=add_generation_prompt,
                enable_thinking=enable_thinking,
            )
            ids = tokenizer.encode(text, add_special_tokens=False)
            return tinker.ModelInput.from_ints(ids)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;tokenizer&#x22;" type="&#x22;Any&#x22;" value="null" />

        <PyParameter name="&#x22;messages&#x22;" type="&#x22;Sequence[Message]&#x22;" value="null" />

        <PyParameter name="&#x22;add_generation_prompt&#x22;" type="&#x22;bool&#x22;" value="&#x22;True&#x22;" />

        <PyParameter name="&#x22;enable_thinking&#x22;" type="&#x22;bool | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="&#x22;tinker.tinker.ModelInput&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;text_to_model_input&#x22;" type="&#x22;(tokenizer, text) -> tinker.ModelInput&#x22;">
      Bypass chat-templating; tokenize `text` verbatim. For callers that
      already have a fully rendered prompt string.

      <PySourceCode>
        ```python
        def text_to_model_input(tokenizer: Any, text: str) -> tinker.ModelInput:
            """Bypass chat-templating; tokenize ``text`` verbatim. For callers that
            already have a fully rendered prompt string."""
            ids = tokenizer.encode(text, add_special_tokens=False)
            return tinker.ModelInput.from_ints(ids)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;tokenizer&#x22;" type="&#x22;Any&#x22;" value="null" />

        <PyParameter name="&#x22;text&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;tinker.tinker.ModelInput&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
