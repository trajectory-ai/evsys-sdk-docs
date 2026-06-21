# ForwardBackwardResult (/docs/evsys_sdk/training/backend/ForwardBackwardResult)



What `forward_backward_*_async` resolves to.

Tinker's own `ForwardBackwardOutput` is opaque (pydantic with no public
attrs in the installed version), so consumers go through the loss\_fn\_outputs
field on the actual response. The Protocol here lists what the loop reads
so the contract is explicit; MockBackend constructs duck-typed objects.

## Attributes [#attributes]

<PyAttribute name="&#x22;loss_fn_outputs&#x22;" type="&#x22;list[dict[str, Any]]&#x22;" value="null">
  One entry per Datum. Each carries algorithm-specific keys —
  `"logprobs"` for cross\_entropy, `"loss"` / `"advantage_sum"` for IS, etc.
</PyAttribute>
