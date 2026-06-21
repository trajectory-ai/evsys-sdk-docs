# TinkerSamplingClient (/docs/evsys_sdk/training/tinker_backend/TinkerSamplingClient)



Wrap a `tinker.SamplingClient` to satisfy the
:class:`~evsys_sdk.training.backend.SamplingClient` Protocol shape.

Same name + same async method signatures as the Protocol, plus a
`raw` attribute for callers that want the underlying tinker client.

## Attributes [#attributes]

<PyAttribute name="&#x22;raw&#x22;" type="null" value="&#x22;raw&#x22;" />

<PyAttribute name="&#x22;name&#x22;" type="null" value="&#x22;name&#x22;" />

<PyAttribute name="&#x22;model_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, raw, *, name='tinker') -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, raw: Any, *, name: str = "tinker") -> None:
        self.raw = raw
        self.name = name
        self.model_path: str | None = None
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;raw&#x22;" type="&#x22;Any&#x22;" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'tinker'&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;sample_async&#x22;" type="&#x22;(self, *, prompt, params, num_samples=1, include_prompt_logprobs=False, topk_prompt_logprobs=0) -> Any&#x22;">
  <PySourceCode>
    ```python
    async def sample_async(
        self,
        *,
        prompt: tinker.ModelInput,
        params: tinker.SamplingParams,
        num_samples: int = 1,
        include_prompt_logprobs: bool = False,
        topk_prompt_logprobs: int = 0,
    ) -> Any:
        kwargs: dict[str, Any] = {
            "prompt": prompt,
            "sampling_params": params,
            "num_samples": num_samples,
        }
        # Tinker's sample_async forwards these only when set; the cookbook does
        # the same conditional inclusion (see distillation/sdft.py:361-369).
        if include_prompt_logprobs:
            kwargs["include_prompt_logprobs"] = True
        if topk_prompt_logprobs > 0:
            kwargs["topk_prompt_logprobs"] = topk_prompt_logprobs
        return await self.raw.sample_async(**kwargs)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;prompt&#x22;" type="&#x22;tinker.ModelInput&#x22;" value="null" />

    <PyParameter name="&#x22;params&#x22;" type="&#x22;tinker.SamplingParams&#x22;" value="null" />

    <PyParameter name="&#x22;num_samples&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />

    <PyParameter name="&#x22;include_prompt_logprobs&#x22;" type="&#x22;bool&#x22;" value="&#x22;False&#x22;" />

    <PyParameter name="&#x22;topk_prompt_logprobs&#x22;" type="&#x22;int&#x22;" value="&#x22;0&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
</PyFunction>

<PyFunction name="&#x22;compute_logprobs_async&#x22;" type="&#x22;(self, prompt) -> list[float | None]&#x22;">
  <PySourceCode>
    ```python
    async def compute_logprobs_async(
        self, prompt: tinker.ModelInput
    ) -> list[float | None]:
        return await self.raw.compute_logprobs_async(prompt)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;prompt&#x22;" type="&#x22;tinker.ModelInput&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;list[float | None]&#x22;" />
</PyFunction>
