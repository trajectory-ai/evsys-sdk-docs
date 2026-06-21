# MockSamplingClient (/docs/evsys_sdk/training/backend/MockSamplingClient)



In-memory SamplingClient for tests.

Returns `canned[i % len(canned)]` per call to `sample_async`. Tests
that need fancy behavior can subclass; the default is enough to exercise
the loop's eval slot.

## Attributes [#attributes]

<PyAttribute name="&#x22;canned&#x22;" type="null" value="&#x22;canned or ['']&#x22;" />

<PyAttribute name="&#x22;name&#x22;" type="null" value="&#x22;name&#x22;" />

<PyAttribute name="&#x22;model_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;sample_calls&#x22;" type="&#x22;list[dict]&#x22;" value="&#x22;[]&#x22;" />

<PyAttribute name="&#x22;logprob_calls&#x22;" type="&#x22;list[tinker.ModelInput]&#x22;" value="&#x22;[]&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, canned=None, name='mock') -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, *, canned: list[str] | None = None, name: str = "mock") -> None:
        self.canned = canned or [""]
        self.name = name
        self.model_path: str | None = None
        self.sample_calls: list[dict] = []
        self.logprob_calls: list[tinker.ModelInput] = []
        self._idx = 0
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;canned&#x22;" type="&#x22;list[str] | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'mock'&#x22;" />
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
        self.sample_calls.append({
            "prompt_len": prompt.length, "params": params, "num_samples": num_samples,
            "topk_prompt_logprobs": topk_prompt_logprobs,
        })
        text = self.canned[self._idx % len(self.canned)]
        self._idx += 1
        # Mimic tinker's SamplingResponse: a .sequences list, each with .tokens.
        seq = _MockResult(tokens=list(range(len(text))) or [0])
        return _MockResult(sequences=[seq], topk_prompt_logprobs=None)
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
    async def compute_logprobs_async(self, prompt: tinker.ModelInput) -> list[float | None]:
        self.logprob_calls.append(prompt)
        return [0.0] * prompt.length
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;prompt&#x22;" type="&#x22;tinker.ModelInput&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;list[float | None]&#x22;" />
</PyFunction>
