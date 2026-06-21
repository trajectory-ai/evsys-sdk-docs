# _CoroFuture (/docs/evsys_sdk/training/tinker_backend/_CoroFuture)



Bridge tinker's coroutine-returning `*_async` methods to the
:class:`~evsys_sdk.training.loop.TrainingLoop` contract.

The loop fires forward\_backward / optim WITHOUT awaiting, then awaits each
one's `result_async()` (the shape :class:`MockBackend` implements). But
tinker's `forward_backward_async` / `optim_step_async` are *coroutine
functions* — calling them returns an un-awaited coroutine, not a future.
This wraps that coroutine so `await wrapper.result_async()` awaits the
coroutine to obtain the tinker future, then awaits the future's result.

## Attributes [#attributes]

<PyAttribute name="&#x22;__slots__&#x22;" type="null" value="&#x22;('_coro',)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, coro) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, coro: Any) -> None:
        self._coro = coro
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;coro&#x22;" type="&#x22;Any&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;result_async&#x22;" type="&#x22;(self) -> Any&#x22;">
  <PySourceCode>
    ```python
    async def result_async(self) -> Any:
        future = await self._coro
        return await future.result_async()
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
</PyFunction>
