# model_eval (/docs/evsys_sdk/eval/model_eval)



Model eval — generate completions over the eval set via an InferenceClient.

Each row in the eval set has 3 queries; the model predicts a tool slug for
each. Pass\@1/pass\@3/pass^3 are computed in :mod:`.report`.

Generation calls are wrapped in the retry helper too — Tinker / remote
inference can occasionally raise transient errors that shouldn't kill the
whole run.

<PyAttribute name="&#x22;DEFAULT_SYSTEM&#x22;" type="null" value="&#x22;'You are a tool search engine. Match user queries to the correct API tool. Think step by step inside <think></think> tags, then give your answer inside <answer></answer> tags.'&#x22;" />

<PyAttribute name="&#x22;DEFAULT_SYSTEM_NO_THINK&#x22;" type="null" value="&#x22;'You are a tool search engine. Match user queries to the correct API tool. Give your answer inside <answer></answer> tags.'&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;PromptBuilder&#x22;" href="&#x22;/docs/evsys_sdk/eval/model_eval/PromptBuilder&#x22;" />

      <Card title="&#x22;ModelEvalConfig&#x22;" href="&#x22;/docs/evsys_sdk/eval/model_eval/ModelEvalConfig&#x22;" />

      <Card title="&#x22;ModelEvalResult&#x22;" href="&#x22;/docs/evsys_sdk/eval/model_eval/ModelEvalResult&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;extract_predicted_slug&#x22;" type="&#x22;(text) -> str&#x22;">
      Pull the predicted slug from `\<answer>...\</answer>` tags;
      fall back to the longest ALL\_CAPS\_TOKEN if tags are missing.

      <PySourceCode>
        ```python
        def extract_predicted_slug(text: str) -> str:
            """Pull the predicted slug from ``<answer>...</answer>`` tags;
            fall back to the longest ALL_CAPS_TOKEN if tags are missing.
            """
            m = _ANSWER_RE.search(text)
            if m:
                return m.group(1).strip()
            candidates = _FALLBACK_SLUG_RE.findall(text)
            return max(candidates, key=len) if candidates else ""
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;text&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;str&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;qwen_chat_prompt&#x22;" type="&#x22;(*, query, toolkit='', expected_slug='') -> str&#x22;">
      Qwen2/Qwen3 chat-template wrapper.

      DEPRECATED hand-built form (missing the auto-injected `\<think>` scaffold
      that Qwen3.5 adds via its chat template). Retained for backwards
      compatibility with older eval runs. New code should use
      :func:`qwen3_chat_template_prompt` which round-trips through
      `apply_chat_template` and supports `enable_thinking`.

      <PySourceCode>
        ```python
        def qwen_chat_prompt(*, query: str, toolkit: str = "", expected_slug: str = "") -> str:
            """Qwen2/Qwen3 chat-template wrapper.

            DEPRECATED hand-built form (missing the auto-injected `<think>` scaffold
            that Qwen3.5 adds via its chat template). Retained for backwards
            compatibility with older eval runs. New code should use
            :func:`qwen3_chat_template_prompt` which round-trips through
            ``apply_chat_template`` and supports ``enable_thinking``.
            """
            return (
                f"<|im_start|>system\n{DEFAULT_SYSTEM}<|im_end|>\n"
                f"<|im_start|>user\nQuery: {query}<|im_end|>\n"
                f"<|im_start|>assistant\n"
            )
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;query&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;toolkit&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

        <PyParameter name="&#x22;expected_slug&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />
      </div>

      <PyFunctionReturn type="&#x22;str&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_get_qwen_tokenizer&#x22;" type="&#x22;(model_name)&#x22;">
      Cached lookup of a HF tokenizer for chat-template rendering.

      <PySourceCode>
        ```python
        def _get_qwen_tokenizer(model_name: str):
            """Cached lookup of a HF tokenizer for chat-template rendering."""
            if model_name not in _QWEN_TOKENIZERS:
                from transformers import AutoTokenizer

                _QWEN_TOKENIZERS[model_name] = AutoTokenizer.from_pretrained(
                    model_name, trust_remote_code=True
                )
            return _QWEN_TOKENIZERS[model_name]
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;model_name&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="null" />
    </PyFunction>

    <PyFunction name="&#x22;qwen3_chat_template_prompt&#x22;" type="&#x22;(*, query, toolkit='', expected_slug='', model_name='Qwen/Qwen3.5-4B', enable_thinking=True, system_prompt=None) -> str&#x22;">
      Build the inference prompt via the model's official chat template.

      For Qwen3-family models, this correctly emits the `\<think>`-scaffold
      suffix matching how the model was trained:

      * `enable_thinking=True`  → prompt ends with `\<|im_start|>assistant\n\<think>\n`
        (model continues from inside the think block).
      * `enable_thinking=False` → prompt ends with
        `\<|im_start|>assistant\n\<think>\n\n\</think>\n\n` (model emits
        `\<answer>` directly).

      <PySourceCode>
        ```python
        def qwen3_chat_template_prompt(
            *,
            query: str,
            toolkit: str = "",
            expected_slug: str = "",
            model_name: str = "Qwen/Qwen3.5-4B",
            enable_thinking: bool = True,
            system_prompt: str | None = None,
        ) -> str:
            """Build the inference prompt via the model's official chat template.

            For Qwen3-family models, this correctly emits the ``<think>``-scaffold
            suffix matching how the model was trained:

            * ``enable_thinking=True``  → prompt ends with ``<|im_start|>assistant\\n<think>\\n``
              (model continues from inside the think block).
            * ``enable_thinking=False`` → prompt ends with
              ``<|im_start|>assistant\\n<think>\\n\\n</think>\\n\\n`` (model emits
              ``<answer>`` directly).
            """
            sys_msg = system_prompt
            if sys_msg is None:
                sys_msg = DEFAULT_SYSTEM if enable_thinking else DEFAULT_SYSTEM_NO_THINK
            tokenizer = _get_qwen_tokenizer(model_name)
            messages = [
                {"role": "system", "content": sys_msg},
                {"role": "user", "content": f"Query: {query}"},
            ]
            return tokenizer.apply_chat_template(
                messages,
                tokenize=False,
                add_generation_prompt=True,
                enable_thinking=enable_thinking,
            )
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;query&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;toolkit&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

        <PyParameter name="&#x22;expected_slug&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

        <PyParameter name="&#x22;model_name&#x22;" type="&#x22;str&#x22;" value="&#x22;'Qwen/Qwen3.5-4B'&#x22;" />

        <PyParameter name="&#x22;enable_thinking&#x22;" type="&#x22;bool&#x22;" value="&#x22;True&#x22;" />

        <PyParameter name="&#x22;system_prompt&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="&#x22;str&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_row_qkeys&#x22;" type="&#x22;(eval_rows) -> list[tuple[int, int, str]]&#x22;">
      <PySourceCode>
        ```python
        def _row_qkeys(eval_rows: list[dict[str, Any]]) -> list[tuple[int, int, str]]:
            out: list[tuple[int, int, str]] = []
            for ri, row in enumerate(eval_rows):
                for qi, q in enumerate(row.get("queries", [])):
                    out.append((ri, qi, q))
            return out
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;eval_rows&#x22;" type="&#x22;list[dict[str, Any]]&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;list[tuple[int, int, str]]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;run_model_eval&#x22;" type="&#x22;(eval_rows, *, client, config=None, progress=True) -> ModelEvalResult&#x22;">
      <PySourceCode>
        ```python
        def run_model_eval(
            eval_rows: list[dict[str, Any]],
            *,
            client: InferenceClient,
            config: ModelEvalConfig | None = None,
            progress: bool = True,
        ) -> ModelEvalResult:
            cfg = config or ModelEvalConfig()
            report = RetryReport()

            # Build all (row,query) prompts up-front so we can choose between
            # sequential or batched submission.
            jobs = _row_qkeys(eval_rows)
            prompts: list[str] = []
            for ri, qi, query in jobs:
                row = eval_rows[ri]
                prompts.append(
                    cfg.prompt_builder(
                        query=query,
                        toolkit=row.get("toolkit", ""),
                        expected_slug=row.get("tool_slug", ""),
                    )
                )

            completions: list[str | None] = [None] * len(jobs)
            use_batch = cfg.batch_size > 1 and hasattr(client, "generate_batch")
            if use_batch:
                # Submit in chunks; retry the whole chunk on transient error.
                idx = 0
                while idx < len(prompts):
                    chunk = prompts[idx : idx + cfg.batch_size]
                    ctx = f"model_gen_batch:{idx}-{idx + len(chunk) - 1}"
                    result = call_with_retry(
                        client.generate_batch,  # type: ignore[attr-defined]
                        prompts=chunk,
                        max_tokens=cfg.max_tokens,
                        temperature=cfg.temperature,
                        max_attempts=cfg.max_attempts,
                        report=report,
                        context=ctx,
                    )
                    if result is None:
                        # Whole batch failed — mark each prompt as retry-exhausted.
                        for j in range(len(chunk)):
                            completions[idx + j] = None
                    else:
                        for j, c in enumerate(result):
                            completions[idx + j] = c
                    idx += cfg.batch_size
                    if progress and idx % (cfg.batch_size * 4) == 0:
                        done = min(idx, len(prompts))
                        print(f"  model eval: {done}/{len(prompts)} prompts done")
            else:
                for i, p in enumerate(prompts):
                    ri, qi, _ = jobs[i]
                    ctx = f"model_gen:row{ri}:q{qi}"
                    completions[i] = call_with_retry(
                        client.generate,
                        prompt=p,
                        max_tokens=cfg.max_tokens,
                        temperature=cfg.temperature,
                        max_attempts=cfg.max_attempts,
                        report=report,
                        context=ctx,
                    )
                    if progress and (i + 1) % 60 == 0:
                        print(f"  model eval: {i + 1}/{len(prompts)} prompts done")

            # Reassemble per-row results.
            out_rows: list[dict[str, Any]] = []
            cursor = 0
            for ri, row in enumerate(eval_rows):
                expected = row.get("tool_slug", "")
                toolkit = row.get("toolkit", "")
                q_outs: list[dict[str, Any]] = []
                for query in row.get("queries", []):
                    comp = completions[cursor]
                    cursor += 1
                    if comp is None:
                        q_outs.append({"query": query, "completion": "", "predicted": "", "error": "retry_exhausted"})
                    else:
                        q_outs.append(
                            {
                                "query": query,
                                "completion": comp,
                                "predicted": extract_predicted_slug(comp),
                                "error": None,
                            }
                        )
                out_rows.append({"tool_slug": expected, "toolkit": toolkit, "queries": q_outs})

            return ModelEvalResult(rows=out_rows, retry_report=report)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;eval_rows&#x22;" type="&#x22;list[dict[str, Any]]&#x22;" value="null" />

        <PyParameter name="&#x22;client&#x22;" type="&#x22;InferenceClient&#x22;" value="null" />

        <PyParameter name="&#x22;config&#x22;" type="&#x22;ModelEvalConfig | None&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;progress&#x22;" type="&#x22;bool&#x22;" value="&#x22;True&#x22;" />
      </div>

      <PyFunctionReturn type="&#x22;evsys_sdk.eval.model_eval.ModelEvalResult&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
