# MockBackend (/docs/evsys_sdk/training/backend/MockBackend)



In-memory Backend for tests.

Records every call to `forward_backward_async` /
`forward_backward_custom_async` / `optim_step_async` /
`save_for_sampler` / `save_full_state` /
`snapshot_sampling_client` for assertion. Returns predictable result
objects so the loop's metric / save / eval paths exercise end-to-end.

## Attributes [#attributes]

<PyAttribute name="&#x22;fb_calls&#x22;" type="&#x22;list[dict]&#x22;" value="&#x22;[]&#x22;" />

<PyAttribute name="&#x22;fb_custom_calls&#x22;" type="&#x22;list[dict]&#x22;" value="&#x22;[]&#x22;" />

<PyAttribute name="&#x22;optim_calls&#x22;" type="&#x22;list[tinker.AdamParams]&#x22;" value="&#x22;[]&#x22;" />

<PyAttribute name="&#x22;save_sampler_calls&#x22;" type="&#x22;list[str]&#x22;" value="&#x22;[]&#x22;" />

<PyAttribute name="&#x22;save_state_calls&#x22;" type="&#x22;list[str]&#x22;" value="&#x22;[]&#x22;" />

<PyAttribute name="&#x22;fb_logprob&#x22;" type="&#x22;float&#x22;" value="&#x22;-0.5&#x22;" />

<PyAttribute name="&#x22;optim_metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="&#x22;{'optim/lr': 0.0001}&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, tokenizer=None, sampler_factory=None) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(
        self,
        *,
        tokenizer: Any = None,
        sampler_factory: Callable[[str], MockSamplingClient] | None = None,
    ) -> None:
        self._tokenizer = tokenizer
        self._sampler_factory = sampler_factory or (lambda name: MockSamplingClient(name=name))
        # call recorders
        self.fb_calls: list[dict] = []
        self.fb_custom_calls: list[dict] = []
        self.optim_calls: list[tinker.AdamParams] = []
        self.save_sampler_calls: list[str] = []
        self.save_state_calls: list[str] = []
        # default per-Datum loss_fn_outputs scalar (used for train_mean_nll math)
        self.fb_logprob: float = -0.5
        # default optim metrics
        self.optim_metrics: dict[str, float] = {"optim/lr": 1e-4}
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;tokenizer&#x22;" type="&#x22;Any&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;sampler_factory&#x22;" type="&#x22;Callable[[str], MockSamplingClient] | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;forward_backward_async&#x22;" type="&#x22;(self, data, *, loss_fn, loss_fn_config=None) -> Any&#x22;">
  <PySourceCode>
    ```python
    def forward_backward_async(
        self,
        data: list[tinker.Datum],
        *,
        loss_fn: tinker.types.LossFnType,
        loss_fn_config: dict[str, Any] | None = None,
    ) -> Any:
        self.fb_calls.append({"n_data": len(data), "loss_fn": loss_fn,
                              "loss_fn_config": loss_fn_config})
        outputs = [{"logprobs": [self.fb_logprob] * 4} for _ in data]
        return _ResolvedFuture(_MockResult(loss_fn_outputs=outputs))
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
  <PySourceCode>
    ```python
    def forward_backward_custom_async(
        self,
        data: list[tinker.Datum],
        loss_fn: LossCallable,
    ) -> Any:
        self.fb_custom_calls.append({"n_data": len(data), "loss_fn": loss_fn})
        outputs = [{"logprobs": [self.fb_logprob] * 4} for _ in data]
        return _ResolvedFuture(_MockResult(loss_fn_outputs=outputs))
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
  <PySourceCode>
    ```python
    def optim_step_async(self, adam: tinker.AdamParams) -> Any:
        self.optim_calls.append(adam)
        return _ResolvedFuture(_MockResult(metrics=dict(self.optim_metrics)))
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;adam&#x22;" type="&#x22;tinker.AdamParams&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
</PyFunction>

<PyFunction name="&#x22;save_for_sampler&#x22;" type="&#x22;(self, name) -> str&#x22;">
  <PySourceCode>
    ```python
    async def save_for_sampler(self, name: str) -> str:
        self.save_sampler_calls.append(name)
        return f"mock://sampler/{name}"
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str&#x22;" />
</PyFunction>

<PyFunction name="&#x22;save_full_state&#x22;" type="&#x22;(self, name) -> str&#x22;">
  <PySourceCode>
    ```python
    async def save_full_state(self, name: str) -> str:
        self.save_state_calls.append(name)
        return f"mock://state/{name}"
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str&#x22;" />
</PyFunction>

<PyFunction name="&#x22;snapshot_sampling_client&#x22;" type="&#x22;(self, name=None) -> SamplingClient&#x22;">
  <PySourceCode>
    ```python
    async def snapshot_sampling_client(self, name: str | None = None) -> SamplingClient:
        label = name or f"snapshot_{len(self.save_sampler_calls)}"
        path = await self.save_for_sampler(label)
        client = self._sampler_factory(label)
        try:
            client.model_path = path  # harbor-backed evaluators re-sample from this
        except Exception:
            pass
        return client
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.training.backend.SamplingClient&#x22;" />
</PyFunction>

<PyFunction name="&#x22;get_tokenizer&#x22;" type="&#x22;(self) -> Any&#x22;">
  <PySourceCode>
    ```python
    def get_tokenizer(self) -> Any:
        return self._tokenizer
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
</PyFunction>
