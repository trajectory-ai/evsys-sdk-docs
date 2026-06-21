# chat_templated (/docs/evsys_sdk/inference/chat_templated)



ChatTemplatedInference — wrap an InferenceClient so eval-time inputs
match the chat-templated distribution the model was trained on.

When a model is SFT'd on chat-templated sequences (HF
`tokenizer.apply_chat_template` over a system + user + assistant message
list), the assistant-side output format only emerges reliably when the
prompt at inference time is shaped the same way. `Benchmark.score()` hands
the client a raw `task.instruction` string with no role markers; this
wrapper rebuilds the (system + user) opener around it before forwarding to
the underlying client.

Project-specific values: the `system_prompt` (verbatim training-time
string) and `user_template` (e.g. `"Query: \{prompt\}"` if user-turn
content has a domain-specific prefix). With `user_template` left at the
default `"\{prompt\}"` the raw instruction passes through unchanged.

Tokenizer-agnostic: any base client exposing a HF-style `_tokenizer`
attribute works (e.g. `TinkerInference`).

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['ChatTemplatedInference']&#x22;" />

<Tabs items="[&#x22;Class&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;ChatTemplatedInference&#x22;" href="&#x22;/docs/evsys_sdk/inference/chat_templated/ChatTemplatedInference&#x22;" />
    </Cards>
  </Tab>
</Tabs>
