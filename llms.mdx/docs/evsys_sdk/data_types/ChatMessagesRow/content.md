# ChatMessagesRow (/docs/evsys_sdk/data_types/ChatMessagesRow)



One SFT conversation — pure data, no supervision encoded.

`messages` is the full multi-turn conversation (roles: system / user /
assistant / tool). The row deliberately carries **no** notion of which
tokens are trained: choosing the supervised span is the *algorithm's* job
(e.g. `SFT` masks assistant turns according to its `supervise`
config). Keeping the dataset format free of target/loss metadata lets the
same conversation feed any SFT variant.

For multimodal SFT, a message's `content` may be either a string OR a
list of content blocks (mix of text + image blocks). See
`image_url_block` / `image_base64_block` helpers below.

## Attributes [#attributes]

<PyAttribute name="&#x22;messages&#x22;" type="&#x22;list[dict]&#x22;" value="null" />

<PyAttribute name="&#x22;metadata&#x22;" type="&#x22;dict&#x22;" value="&#x22;field(default_factory=dict)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, messages, metadata=dict()) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;messages&#x22;" type="&#x22;list[dict]&#x22;" value="null" />

    <PyParameter name="&#x22;metadata&#x22;" type="&#x22;dict&#x22;" value="&#x22;dict()&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
