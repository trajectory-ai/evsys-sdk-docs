# Backend (/docs/evsys_sdk/training/backend/Backend)



Wraps a training client + factory for sampling clients.

A `Backend` is constructed once per run (typically by the Algorithm
composer, see :mod:`evsys_sdk.algorithms.sft`). It owns the
live training client; everything else in the package operates against
this Protocol.

## Functions [#functions]

<PyFunction name="&#x22;forward_backward_async&#x22;" type="&#x22;(self, data, *, loss_fn, loss_fn_config=None) -> Any&#x22;">
  Submit a forward + backward pass with a named server-side loss.

  Returns an awaitable that resolves to a :class:`ForwardBackwardResult`.
  Tinker's loss names are `"cross_entropy"` (with per-position weights
  on Datum.loss\_fn\_inputs\["weights"]) and `"importance_sampling"`
  (with advantages on Datum.loss\_fn\_inputs\["advantages"]).

  <PySourceCode>
    ```python
    def forward_backward_async(
        self,
        data: list[tinker.Datum],
        *,
        loss_fn: tinker.types.LossFnType,
        loss_fn_config: dict[str, Any] | None = None,
    ) -> Any:
        """Submit a forward + backward pass with a named server-side loss.

        Returns an awaitable that resolves to a :class:`ForwardBackwardResult`.
        Tinker's loss names are ``"cross_entropy"`` (with per-position weights
        on Datum.loss_fn_inputs["weights"]) and ``"importance_sampling"``
        (with advantages on Datum.loss_fn_inputs["advantages"]).
        """
        ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;data&#x22;" type="&#x22;list[tinker.Datum]&#x22;" value="null" />

    <PyParameter name="&#x22;loss_fn&#x22;" type="&#x22;tinker.types.LossFnType&#x22;" value="null" />

    <PyParameter name="&#x22;loss_fn_config&#x22;" type="&#x22;dict[str, Any] | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
</PyFunction>

<PyFunction name="&#x22;forward_backward_custom_async&#x22;" type="&#x22;(self, data, loss_fn) -> Any&#x22;">
  Same as :meth:`forward_backward_async`, but the loss is a
  client-side Python callable.

  Tinker streams logits/logprobs back, the callable runs locally on
  each Datum, and the returned scalar gets sent back for the backward
  pass. This is the same hook SDFT uses for analytical reverse-KL,
  exposed here as a first-class extension point.

  <PySourceCode>
    ```python
    def forward_backward_custom_async(
        self,
        data: list[tinker.Datum],
        loss_fn: LossCallable,
    ) -> Any:
        """Same as :meth:`forward_backward_async`, but the loss is a
        client-side Python callable.

        Tinker streams logits/logprobs back, the callable runs locally on
        each Datum, and the returned scalar gets sent back for the backward
        pass. This is the same hook SDFT uses for analytical reverse-KL,
        exposed here as a first-class extension point.
        """
        ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;data&#x22;" type="&#x22;list[tinker.Datum]&#x22;" value="null" />

    <PyParameter name="&#x22;loss_fn&#x22;" type="&#x22;LossCallable&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
</PyFunction>

<PyFunction name="&#x22;optim_step_async&#x22;" type="&#x22;(self, adam) -> Any&#x22;">
  Optimizer step. Returns an awaitable resolving to :class:`OptimStepResult`.

  <PySourceCode>
    ```python
    def optim_step_async(self, adam: tinker.AdamParams) -> Any:
        """Optimizer step. Returns an awaitable resolving to :class:`OptimStepResult`."""
        ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;adam&#x22;" type="&#x22;tinker.AdamParams&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
</PyFunction>

<PyFunction name="&#x22;save_for_sampler&#x22;" type="&#x22;(self, name) -> str&#x22;">
  Snapshot the current weights for inference. Returns the sampler URI.

  <PySourceCode>
    ```python
    async def save_for_sampler(self, name: str) -> str:
        """Snapshot the current weights for inference. Returns the sampler URI."""
        ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str&#x22;" />
</PyFunction>

<PyFunction name="&#x22;save_full_state&#x22;" type="&#x22;(self, name) -> str&#x22;">
  Snapshot weights + optimizer state for resume. Returns the URI.

  <PySourceCode>
    ```python
    async def save_full_state(self, name: str) -> str:
        """Snapshot weights + optimizer state for resume. Returns the URI."""
        ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str&#x22;" />
</PyFunction>

<PyFunction name="&#x22;snapshot_sampling_client&#x22;" type="&#x22;(self, name=None) -> SamplingClient&#x22;">
  Save\_weights\_for\_sampler + construct an inference client at that URI.

  Convenience for the in-loop eval slot, which needs both: save the
  latest weights AND get a client that can sample from them. The
  manager records the URI; the eval slot uses the client.

  <PySourceCode>
    ```python
    async def snapshot_sampling_client(self, name: str | None = None) -> SamplingClient:
        """Save_weights_for_sampler + construct an inference client at that URI.

        Convenience for the in-loop eval slot, which needs both: save the
        latest weights AND get a client that can sample from them. The
        manager records the URI; the eval slot uses the client.
        """
        ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.training.backend.SamplingClient&#x22;" />
</PyFunction>

<PyFunction name="&#x22;get_tokenizer&#x22;" type="&#x22;(self) -> Any&#x22;">
  HF tokenizer the backend is using. Used by StepBuilders that need
  to tokenize text (SFT) or apply chat templates (SDFT).

  <PySourceCode>
    ```python
    def get_tokenizer(self) -> Any:
        """HF tokenizer the backend is using. Used by StepBuilders that need
        to tokenize text (SFT) or apply chat templates (SDFT)."""
        ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
</PyFunction>
