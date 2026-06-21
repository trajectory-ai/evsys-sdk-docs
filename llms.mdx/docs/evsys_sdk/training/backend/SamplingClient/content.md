# SamplingClient (/docs/evsys_sdk/training/backend/SamplingClient)



Inference-only client backed by a saved sampler checkpoint.

Created by :meth:`Backend.snapshot_sampling_client`. Both the loop's
in-loop eval slot and any rollout-based StepBuilder (SDFT, RL) consume
this surface — never the underlying tinker client directly.

## Functions [#functions]

<PyFunction name="&#x22;sample_async&#x22;" type="&#x22;(self, *, prompt, params, num_samples=1, include_prompt_logprobs=False, topk_prompt_logprobs=0) -> Any&#x22;">
  Generate tokens for `prompt`. Return shape is backend-defined.

  TinkerBackend returns tinker's native `SamplingResponse` with
  `.sequences` (one per sample) and optional `.topk_prompt_logprobs`.
  MockBackend returns canned strings for tests.

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
        """Generate tokens for ``prompt``. Return shape is backend-defined.

        TinkerBackend returns tinker's native ``SamplingResponse`` with
        ``.sequences`` (one per sample) and optional ``.topk_prompt_logprobs``.
        MockBackend returns canned strings for tests.
        """
        ...
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
  Per-position logprobs of the prompt under the current weights.

  <PySourceCode>
    ```python
    async def compute_logprobs_async(self, prompt: tinker.ModelInput) -> list[float | None]:
        """Per-position logprobs of the prompt under the current weights."""
        ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;prompt&#x22;" type="&#x22;tinker.ModelInput&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;list[float | None]&#x22;" />
</PyFunction>
