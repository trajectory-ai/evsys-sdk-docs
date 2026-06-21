# tinker_backend (/docs/evsys_sdk/training/tinker_backend)



TinkerBackend — concrete :class:`~evsys_sdk.training.backend.Backend`
implementation against `tinker.ServiceClient`.

Constructed via `await TinkerBackend.create(model_name=..., lora_rank=...)`
— the underlying `create_lora_training_client_async` is async, so the
factory is async. Algorithm composers run the loop inside one
`asyncio.run(...)` call so the constructor and the loop body share the
same event loop:

async def \_train(self, ctx):
backend = await TinkerBackend.create(...)
loop = TrainingLoop(backend=backend, ...)
return await loop.run(num\_steps=cfg.max\_steps)

def train(self, ctx):
return asyncio.run(self.\_train(ctx))

The wrapper is thin — every method is a one-to-one passthrough to the
underlying tinker client, plus the one ergonomic affordance of returning
a wrapped :class:`TinkerSamplingClient` from
`snapshot_sampling_client` (so callers don't have to know about the
sampler URI machinery).

<PyAttribute name="&#x22;logger&#x22;" type="null" value="&#x22;logging.getLogger(__name__)&#x22;" />

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['TinkerBackend', 'TinkerSamplingClient']&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;_CoroFuture&#x22;" href="&#x22;/docs/evsys_sdk/training/tinker_backend/_CoroFuture&#x22;" />

      <Card title="&#x22;TinkerSamplingClient&#x22;" href="&#x22;/docs/evsys_sdk/training/tinker_backend/TinkerSamplingClient&#x22;" />

      <Card title="&#x22;TinkerBackend&#x22;" href="&#x22;/docs/evsys_sdk/training/tinker_backend/TinkerBackend&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;_result_path&#x22;" type="&#x22;(result) -> str&#x22;">
      Tinker's save\_\*\_async result envelopes carry a `.path` attribute
      (verified against cookbook usage in distillation/sdft.py:739, where the
      same dotted access ships).

      <PySourceCode>
        ```python
        def _result_path(result: Any) -> str:
            """Tinker's save_*_async result envelopes carry a ``.path`` attribute
            (verified against cookbook usage in distillation/sdft.py:739, where the
            same dotted access ships)."""
            path = getattr(result, "path", None)
            if not path:
                raise RuntimeError(
                    f"tinker save result missing .path attribute (got {result!r})"
                )
            return str(path)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;result&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;str&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
