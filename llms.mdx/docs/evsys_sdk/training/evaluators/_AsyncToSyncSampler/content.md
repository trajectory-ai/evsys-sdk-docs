# _AsyncToSyncSampler (/docs/evsys_sdk/training/evaluators/_AsyncToSyncSampler)



Sync :class:`~evsys_sdk.protocols.InferenceClient` over an async
:class:`~evsys_sdk.training.backend.SamplingClient`.

`.generate(prompt=...)` is called sequentially by
:meth:`Benchmark.score` from a worker thread; we route the awaited
`sample_async` call back to the main event loop via
:func:`asyncio.run_coroutine_threadsafe` so the inner async work
runs on the loop that owns the live training client.

Exposes `_tokenizer` so :class:`ChatTemplatedInference` can wrap
this client without further plumbing — same shape
:class:`TinkerInference` exposes.

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'in_loop_sampler'&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, sampler, tokenizer, loop) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, sampler: Any, tokenizer: Any, loop: asyncio.AbstractEventLoop) -> None:
        self._sampler = sampler
        self._tokenizer = tokenizer
        self._loop = loop
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;sampler&#x22;" type="&#x22;Any&#x22;" value="null" />

    <PyParameter name="&#x22;tokenizer&#x22;" type="&#x22;Any&#x22;" value="null" />

    <PyParameter name="&#x22;loop&#x22;" type="&#x22;asyncio.AbstractEventLoop&#x22;" value="null" />
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
        ids = self._tokenizer.encode(prompt, add_special_tokens=False)
        prompt_mi = tinker.ModelInput.from_ints(ids)
        params = tinker.SamplingParams(
            max_tokens=max_tokens,
            temperature=temperature,
            stop=stop or [],
        )
        coro = self._sampler.sample_async(prompt=prompt_mi, params=params)
        fut = asyncio.run_coroutine_threadsafe(coro, self._loop)
        response = fut.result()
        return self._decode(response)
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

<PyFunction name="&#x22;_decode&#x22;" type="&#x22;(self, response) -> str&#x22;">
  <PySourceCode>
    ```python
    def _decode(self, response: Any) -> str:
        seqs = getattr(response, "sequences", None)
        if not seqs:
            return ""
        first = seqs[0]
        tokens = (
            getattr(first, "tokens", None) or getattr(first, "token_ids", None) or []
        )
        if not tokens:
            return ""
        return self._tokenizer.decode(list(tokens))
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;response&#x22;" type="&#x22;Any&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str&#x22;" />
</PyFunction>
