# JSONLToChatTransform (/docs/evsys_sdk/transforms/jsonl_to_chat/JSONLToChatTransform)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'jsonl_to_chat'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;JSONLToChatConfig&#x22;" />

<PyAttribute name="&#x22;system_prompt&#x22;" type="null" value="&#x22;system_prompt&#x22;" />

<PyAttribute name="&#x22;user_template&#x22;" type="null" value="&#x22;user_template&#x22;" />

<PyAttribute name="&#x22;assistant_template&#x22;" type="null" value="&#x22;assistant_template&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, system_prompt='', user_template='{query}', assistant_template=None) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(
        self,
        *,
        system_prompt: str = "",
        user_template: str = "{query}",
        assistant_template: str | None = None,
    ) -> None:
        self.system_prompt = system_prompt
        self.user_template = user_template
        self.assistant_template = assistant_template
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;system_prompt&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

    <PyParameter name="&#x22;user_template&#x22;" type="&#x22;str&#x22;" value="&#x22;'{query}'&#x22;" />

    <PyParameter name="&#x22;assistant_template&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;__call__&#x22;" type="&#x22;(self, rows) -> Iterable[dict[str, Any]]&#x22;">
  <PySourceCode>
    ```python
    def __call__(self, rows: Iterable[dict[str, Any]]) -> Iterable[dict[str, Any]]:
        for row in rows:
            messages: list[dict[str, str]] = []
            if self.system_prompt:
                messages.append({"role": "system", "content": self.system_prompt})
            messages.append(
                {"role": "user", "content": self.user_template.format(**row)}
            )
            if self.assistant_template is not None:
                messages.append(
                    {"role": "assistant", "content": self.assistant_template.format(**row)}
                )
            yield {**row, "messages": messages}
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;rows&#x22;" type="&#x22;Iterable[dict[str, Any]]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Iterable[dict[str, typing.Any]]&#x22;" />
</PyFunction>
