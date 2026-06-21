# data_types (/docs/evsys_sdk/data_types)



Harbor-compatible data shapes.

The schemas runners consume and dashboards render. Mirrors the production
internal types so JSONL files round-trip between this SDK and the internal
serving / dashboard stack without conversion.

Two row formats:

* `ChatMessagesRow`  — SFT example  (messages prefix + supervised target)
* `HarborTask`       — RL task      (instruction + verifier spec)

Verifier specs (the data the runner serializes for HarborTask):

* `InProcessVerifier` — cheap Python fn lookup
* `E2BVerifier`       — sandboxed code execution
* `LLMJudgeVerifier`  — judge model + rubric
* `VerifierPayload`   — discriminated union of the three (use this in type hints).

These are *data shapes* (Pydantic / dataclasses) describing the verification
plan; the runtime `Verifier` Protocol in `protocols.py` is what actually
EXECUTES verification. They are deliberately separate concepts.

Multimodal content: messages can carry text + image blocks in OpenAI or
Anthropic style. Use `image_url_block(url)` or
`image_base64_block(media_type, b64)` for convenience.

<PyAttribute name="&#x22;VerifierPayload&#x22;" type="null" value="&#x22;Union[InProcessVerifier, E2BVerifier, LLMJudgeVerifier]&#x22;">
  Discriminated by `.kind` ∈ \{'in\_process', 'e2b', 'llm\_judge'}.
</PyAttribute>

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['TargetFormat', 'ChatMessagesRow', 'HarborTask', 'PromptExample', 'InProcessVerifier', 'E2BVerifier', 'LLMJudgeVerifier', 'VerifierPayload', 'text_block', 'image_url_block', 'image_base64_block', 'block_to_image_src', 'has_images', 'detect_format', 'harbor_task_from_dict', 'chat_messages_row_from_dict', 'prompt_example_from_dict', 'from_dict', 'parse_rows', 'to_dict', 'iter_jsonl']&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;TargetFormat&#x22;" href="&#x22;/docs/evsys_sdk/data_types/TargetFormat&#x22;" />

      <Card title="&#x22;ChatMessagesRow&#x22;" href="&#x22;/docs/evsys_sdk/data_types/ChatMessagesRow&#x22;" />

      <Card title="&#x22;InProcessVerifier&#x22;" href="&#x22;/docs/evsys_sdk/data_types/InProcessVerifier&#x22;" />

      <Card title="&#x22;E2BVerifier&#x22;" href="&#x22;/docs/evsys_sdk/data_types/E2BVerifier&#x22;" />

      <Card title="&#x22;LLMJudgeVerifier&#x22;" href="&#x22;/docs/evsys_sdk/data_types/LLMJudgeVerifier&#x22;" />

      <Card title="&#x22;HarborTask&#x22;" href="&#x22;/docs/evsys_sdk/data_types/HarborTask&#x22;" />

      <Card title="&#x22;PromptExample&#x22;" href="&#x22;/docs/evsys_sdk/data_types/PromptExample&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;text_block&#x22;" type="&#x22;(text) -> dict&#x22;">
      Standard text block. Works in either OpenAI or Anthropic shape.

      <PySourceCode>
        ```python
        def text_block(text: str) -> dict:
            """Standard text block. Works in either OpenAI or Anthropic shape."""
            return {"type": "text", "text": text}
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;text&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;dict&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;image_url_block&#x22;" type="&#x22;(url, *, detail=None) -> dict&#x22;">
      OpenAI-style image block. Works for any vision model that follows the
      OpenAI multimodal content schema (most do).

      <PySourceCode>
        ```python
        def image_url_block(url: str, *, detail: str | None = None) -> dict:
            """OpenAI-style image block. Works for any vision model that follows the
            OpenAI multimodal content schema (most do)."""
            iu: dict = {"url": url}
            if detail:
                iu["detail"] = detail
            return {"type": "image_url", "image_url": iu}
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;url&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;detail&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="&#x22;dict&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;image_base64_block&#x22;" type="&#x22;(media_type, b64_data) -> dict&#x22;">
      Anthropic-style base64 image block.

      `media_type` is the MIME type, e.g. `"image/png"`.

      <PySourceCode>
        ```python
        def image_base64_block(media_type: str, b64_data: str) -> dict:
            """Anthropic-style base64 image block.

            ``media_type`` is the MIME type, e.g. ``"image/png"``.
            """
            return {
                "type": "image",
                "source": {"type": "base64", "media_type": media_type, "data": b64_data},
            }
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;media_type&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;b64_data&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;dict&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;block_to_image_src&#x22;" type="&#x22;(block) -> str | None&#x22;">
      Return a renderable image src URL/data-URL for a content block, or None.

      Handles OpenAI `image_url` blocks AND Anthropic `image` blocks (both
      URL and base64 variants). Used by dashboard renderers.

      <PySourceCode>
        ```python
        def block_to_image_src(block: Any) -> str | None:
            """Return a renderable image src URL/data-URL for a content block, or None.

            Handles OpenAI ``image_url`` blocks AND Anthropic ``image`` blocks (both
            URL and base64 variants). Used by dashboard renderers.
            """
            if not isinstance(block, dict):
                return None
            if block.get("type") == "image_url":
                iu = block.get("image_url") or {}
                url = iu.get("url")
                if isinstance(url, str):
                    return url
            if block.get("type") == "image" and isinstance(block.get("source"), dict):
                src = block["source"]
                if src.get("type") == "url" and isinstance(src.get("url"), str):
                    return src["url"]
                if (
                    src.get("type") == "base64"
                    and isinstance(src.get("data"), str)
                    and isinstance(src.get("media_type"), str)
                ):
                    return f"data:{src['media_type']};base64,{src['data']}"
            return None
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;block&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;str | None&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;has_images&#x22;" type="&#x22;(row) -> bool&#x22;">
      True iff any message in the row carries at least one image block.

      <PySourceCode>
        ```python
        def has_images(row: ChatMessagesRow) -> bool:
            """True iff any message in the row carries at least one image block."""
            for m in row.messages:
                c = m.get("content") if isinstance(m, dict) else None
                if isinstance(c, list):
                    for b in c:
                        if block_to_image_src(b) is not None:
                            return True
            return False
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;row&#x22;" type="&#x22;ChatMessagesRow&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;bool&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;detect_format&#x22;" type="&#x22;(row) -> str&#x22;">
      Returns 'chat\_messages' | 'harbor\_task' | 'prompt\_dataset' | 'unknown'.

      Checked most-specific first: `harbor_task` and `prompt_dataset` have
      distinctive key pairs; `chat_messages` is any row carrying `messages`
      (the conversation — supervision is decided by the algorithm, not the row).

      <PySourceCode>
        ```python
        def detect_format(row: Any) -> str:
            """Returns 'chat_messages' | 'harbor_task' | 'prompt_dataset' | 'unknown'.

            Checked most-specific first: ``harbor_task`` and ``prompt_dataset`` have
            distinctive key pairs; ``chat_messages`` is any row carrying ``messages``
            (the conversation — supervision is decided by the algorithm, not the row).
            """
            if not isinstance(row, dict):
                return "unknown"
            if "task_id" in row and "verifier" in row:
                return "harbor_task"
            if "inputs" in row and "expected" in row:
                return "prompt_dataset"
            if "messages" in row:
                return "chat_messages"
            return "unknown"
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;row&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;str&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_verifier_from_dict&#x22;" type="&#x22;(d) -> VerifierPayload&#x22;">
      <PySourceCode>
        ```python
        def _verifier_from_dict(d: dict) -> VerifierPayload:
            kind = d.get("kind")
            if kind == "in_process":
                return InProcessVerifier(
                    fn_name=d["fn_name"],
                    expected=d.get("expected"),
                    params=dict(d.get("params") or {}),
                )
            if kind == "e2b":
                return E2BVerifier(
                    dockerfile=d.get("dockerfile", ""),
                    test_sh=d.get("test_sh", ""),
                    test_state_py=d.get("test_state_py", ""),
                )
            if kind == "llm_judge":
                return LLMJudgeVerifier(
                    judge_model=d.get("judge_model", ""),
                    rubric=d.get("rubric", ""),
                )
            raise ValueError(f"unknown verifier kind: {kind!r}")
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;d&#x22;" type="&#x22;dict&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;evsys_sdk.data_types.VerifierPayload&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;harbor_task_from_dict&#x22;" type="&#x22;(d) -> HarborTask&#x22;">
      <PySourceCode>
        ```python
        def harbor_task_from_dict(d: dict) -> HarborTask:
            return HarborTask(
                task_id=d["task_id"],
                instruction=d["instruction"],
                verifier=_verifier_from_dict(d["verifier"]),
                metadata=dict(d.get("metadata") or {}),
            )
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;d&#x22;" type="&#x22;dict&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;evsys_sdk.data_types.HarborTask&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;chat_messages_row_from_dict&#x22;" type="&#x22;(d) -> ChatMessagesRow&#x22;">
      <PySourceCode>
        ```python
        def chat_messages_row_from_dict(d: dict) -> ChatMessagesRow:
            msgs = d.get("messages")
            if not isinstance(msgs, list) or not msgs:
                raise ValueError("chat_messages row requires a non-empty `messages` list")
            return ChatMessagesRow(
                messages=list(msgs),
                metadata=dict(d.get("metadata") or {}),
            )
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;d&#x22;" type="&#x22;dict&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;evsys_sdk.data_types.ChatMessagesRow&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;prompt_example_from_dict&#x22;" type="&#x22;(d) -> PromptExample&#x22;">
      <PySourceCode>
        ```python
        def prompt_example_from_dict(d: dict) -> PromptExample:
            return PromptExample(
                inputs=dict(d.get("inputs") or {}),
                expected=d.get("expected"),
                metadata=dict(d.get("metadata") or {}),
            )
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;d&#x22;" type="&#x22;dict&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;evsys_sdk.data_types.PromptExample&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;from_dict&#x22;" type="&#x22;(row) -> Union[ChatMessagesRow, HarborTask, PromptExample]&#x22;">
      Dispatch on row shape — round-trips the JSONL coming off a runner.

      <PySourceCode>
        ```python
        def from_dict(row: dict) -> Union[ChatMessagesRow, HarborTask, PromptExample]:
            """Dispatch on row shape — round-trips the JSONL coming off a runner."""
            fmt = detect_format(row)
            if fmt == "chat_messages":  return chat_messages_row_from_dict(row)
            if fmt == "harbor_task":    return harbor_task_from_dict(row)
            if fmt == "prompt_dataset": return prompt_example_from_dict(row)
            raise ValueError(f"can't dispatch row — unknown format: keys={sorted(row.keys())[:6]}")
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;row&#x22;" type="&#x22;dict&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;typing.Union[evsys_sdk.data_types.ChatMessagesRow, evsys_sdk.data_types.HarborTask, evsys_sdk.data_types.PromptExample]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;parse_rows&#x22;" type="&#x22;(rows, fmt) -> list[Union[ChatMessagesRow, HarborTask, PromptExample]]&#x22;">
      Strictly parse raw dicts into typed rows for the given `fmt`.

      This is the standardized boundary between the transform stage and a
      StepBuilder/algorithm: `raw rows -> transforms -> parse_rows(fmt) -> typed
      rows`. `fmt` is a :class:`TargetFormat` (or its string value). Every row
      must match `fmt` per :func:`detect_format`; a mismatch raises
      `ValueError` naming the offending row — no silent coercion, mirroring the
      SDK's `extra='forbid'` philosophy. Tokenization/rollout stays downstream,
      owned by the algorithm.

      <PySourceCode>
        ```python
        def parse_rows(
            rows: Iterable[dict],
            fmt: Union["TargetFormat", str],
        ) -> list[Union[ChatMessagesRow, HarborTask, PromptExample]]:
            """Strictly parse raw dicts into typed rows for the given ``fmt``.

            This is the standardized boundary between the transform stage and a
            StepBuilder/algorithm: ``raw rows -> transforms -> parse_rows(fmt) -> typed
            rows``. ``fmt`` is a :class:`TargetFormat` (or its string value). Every row
            must match ``fmt`` per :func:`detect_format`; a mismatch raises
            ``ValueError`` naming the offending row — no silent coercion, mirroring the
            SDK's ``extra='forbid'`` philosophy. Tokenization/rollout stays downstream,
            owned by the algorithm.
            """
            want = fmt.value if isinstance(fmt, TargetFormat) else str(fmt)
            parser = _ROW_PARSERS.get(want)
            if parser is None:
                raise ValueError(
                    f"parse_rows: unsupported target format {want!r} "
                    f"(expected one of {sorted(_ROW_PARSERS)})"
                )
            out: list[Union[ChatMessagesRow, HarborTask, PromptExample]] = []
            for i, r in enumerate(rows):
                got = detect_format(r)
                if got != want:
                    keys = sorted(r)[:6] if isinstance(r, dict) else type(r).__name__
                    raise ValueError(
                        f"parse_rows: row {i} has format {got!r}, expected {want!r} "
                        f"(keys={keys})"
                    )
                out.append(parser(r))
            return out
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;rows&#x22;" type="&#x22;Iterable[dict]&#x22;" value="null" />

        <PyParameter name="&#x22;fmt&#x22;" type="&#x22;Union['TargetFormat', str]&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;list[typing.Union[evsys_sdk.data_types.ChatMessagesRow, evsys_sdk.data_types.HarborTask, evsys_sdk.data_types.PromptExample]]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;to_dict&#x22;" type="&#x22;(obj) -> dict&#x22;">
      Dataclass → plain dict (JSON-serializable).

      <PySourceCode>
        ```python
        def to_dict(
            obj: Union[ChatMessagesRow, HarborTask, PromptExample, VerifierPayload],
        ) -> dict:
            """Dataclass → plain dict (JSON-serializable)."""
            import dataclasses as _dc
            return _dc.asdict(obj)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;obj&#x22;" type="&#x22;Union[ChatMessagesRow, HarborTask, PromptExample, VerifierPayload]&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;dict&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;iter_jsonl&#x22;" type="&#x22;(path) -> Iterable[Union[ChatMessagesRow, HarborTask, PromptExample]]&#x22;">
      Iterate a mixed-format JSONL and yield typed rows.

      <PySourceCode>
        ```python
        def iter_jsonl(path: str) -> Iterable[Union[ChatMessagesRow, HarborTask, PromptExample]]:
            """Iterate a mixed-format JSONL and yield typed rows."""
            import json
            with open(path) as f:
                for line in f:
                    line = line.strip()
                    if not line:
                        continue
                    yield from_dict(json.loads(line))
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;path&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;typing.Iterable[typing.Union[evsys_sdk.data_types.ChatMessagesRow, evsys_sdk.data_types.HarborTask, evsys_sdk.data_types.PromptExample]]&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
