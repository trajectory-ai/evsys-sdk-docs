# backend (/docs/evsys_sdk/training/backend)



Backend — the only surface that talks to tinker.

The `Backend` Protocol is the seam the rest of the training stack is built
against. A concrete backend (:class:`~evsys_sdk.training.backend.MockBackend`
for tests; the future `TinkerBackend` for real runs) implements the
six-method contract and the rest of the package (loop, step builders,
checkpoint manager) stays backend-agnostic. That's how we drop the
tinker\_cookbook dependency without locking ourselves to tinker either.

Method shape mirrors tinker's own client so a TinkerBackend implementation
is one-to-one plumbing — see the docstrings on each method for the
contract.

<PyAttribute name="&#x22;LossCallable&#x22;" type="null" value="&#x22;Callable[[Any, dict[str, Any]], Any]&#x22;">
  Custom client-side loss. Receives `(model_output, batch_metadata)` and
  returns a scalar (typically a `torch.Tensor`) the backend uses for the
  backward pass. The exact shape of `model_output` is backend-defined —
  TinkerBackend passes `tinker.ForwardOutput` here.
</PyAttribute>

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['Backend', 'ForwardBackwardResult', 'LossCallable', 'MockBackend', 'MockSamplingClient', 'OptimStepResult', 'SamplingClient']&#x22;" />

<Tabs items="[&#x22;Class&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;ForwardBackwardResult&#x22;" href="&#x22;/docs/evsys_sdk/training/backend/ForwardBackwardResult&#x22;" />

      <Card title="&#x22;OptimStepResult&#x22;" href="&#x22;/docs/evsys_sdk/training/backend/OptimStepResult&#x22;" />

      <Card title="&#x22;SamplingClient&#x22;" href="&#x22;/docs/evsys_sdk/training/backend/SamplingClient&#x22;" />

      <Card title="&#x22;Backend&#x22;" href="&#x22;/docs/evsys_sdk/training/backend/Backend&#x22;" />

      <Card title="&#x22;_ResolvedFuture&#x22;" href="&#x22;/docs/evsys_sdk/training/backend/_ResolvedFuture&#x22;" />

      <Card title="&#x22;_MockResult&#x22;" href="&#x22;/docs/evsys_sdk/training/backend/_MockResult&#x22;" />

      <Card title="&#x22;MockSamplingClient&#x22;" href="&#x22;/docs/evsys_sdk/training/backend/MockSamplingClient&#x22;" />

      <Card title="&#x22;MockBackend&#x22;" href="&#x22;/docs/evsys_sdk/training/backend/MockBackend&#x22;" />
    </Cards>
  </Tab>
</Tabs>
