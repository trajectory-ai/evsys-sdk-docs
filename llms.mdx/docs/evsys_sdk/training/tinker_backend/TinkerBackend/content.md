# TinkerBackend (/docs/evsys_sdk/training/tinker_backend/TinkerBackend)



Implements :class:`~evsys_sdk.training.backend.Backend` over tinker.

Construct via the :meth:`create` async factory. Sync method calls
(`forward_backward_async`, `optim_step_async`,
`forward_backward_custom_async`) return the underlying tinker
`APIFuture` directly so the loop's existing `.result_async()`
awaits work unchanged.

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, service_client, training_client, model_name, tokenizer) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(
        self,
        *,
        service_client: Any,
        training_client: Any,
        model_name: str,
        tokenizer: Any,
    ) -> None:
        self._service = service_client
        self._training = training_client
        self._model_name = model_name
        self._tokenizer = tokenizer
        self._save_counter = 0
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;service_client&#x22;" type="&#x22;Any&#x22;" value="null" />

    <PyParameter name="&#x22;training_client&#x22;" type="&#x22;Any&#x22;" value="null" />

    <PyParameter name="&#x22;model_name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;tokenizer&#x22;" type="&#x22;Any&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;create&#x22;" type="&#x22;(cls, *, model_name, lora_rank=32, renderer_name=None, resume_state_path=None, init_weights_path=None, api_key_env='TINKER_API_KEY', user_metadata=None) -> 'TinkerBackend'&#x22;">
  Async factory.

  `resume_state_path`: when provided, the training client is created
  from that prior `state_path` *with* optimizer state (full resume).
  `init_weights_path`: load *weights only* from that prior state path
  and start a fresh optimizer (used to chain continual-learning stages).
  When both are `None`, a fresh LoRA training client is allocated at
  `model_name` with `lora_rank`. `resume_state_path` wins if both
  are set.

  `renderer_name` is recorded onto the underlying training client's
  user metadata so the inference path can read it back from a
  checkpoint manifest (same convention tinker\_cookbook used).

  <PySourceCode>
    ```python
    @classmethod
    async def create(
        cls,
        *,
        model_name: str,
        lora_rank: int = 32,
        renderer_name: str | None = None,
        resume_state_path: str | None = None,
        init_weights_path: str | None = None,
        api_key_env: str = "TINKER_API_KEY",
        user_metadata: dict[str, str] | None = None,
    ) -> "TinkerBackend":
        """Async factory.

        ``resume_state_path``: when provided, the training client is created
        from that prior ``state_path`` *with* optimizer state (full resume).
        ``init_weights_path``: load *weights only* from that prior state path
        and start a fresh optimizer (used to chain continual-learning stages).
        When both are ``None``, a fresh LoRA training client is allocated at
        ``model_name`` with ``lora_rank``. ``resume_state_path`` wins if both
        are set.

        ``renderer_name`` is recorded onto the underlying training client's
        user metadata so the inference path can read it back from a
        checkpoint manifest (same convention tinker_cookbook used).
        """
        if not os.environ.get(api_key_env):
            raise RuntimeError(f"{api_key_env} not set in environment")
        service = tinker.ServiceClient()
        meta = dict(user_metadata or {})
        if renderer_name:
            meta["renderer_name"] = renderer_name

        if resume_state_path:
            training = await service.create_training_client_from_state_with_optimizer_async(
                resume_state_path, user_metadata=meta or None,
            )
            logger.info("TinkerBackend: resumed (with optimizer) from %s", resume_state_path)
        elif init_weights_path:
            # Weights only: fresh optimizer. Used by continual learning so each
            # stage continues from the prior stage's weights without inheriting
            # its optimizer moments.
            training = await service.create_training_client_from_state_async(
                init_weights_path, user_metadata=meta or None,
            )
            logger.info("TinkerBackend: init weights (fresh optimizer) from %s", init_weights_path)
        else:
            training = await service.create_lora_training_client_async(
                model_name, rank=lora_rank, user_metadata=meta or None,
            )
            logger.info("TinkerBackend: created LoRA client model=%s rank=%d",
                        model_name, lora_rank)

        tokenizer = get_tokenizer(model_name)
        return cls(
            service_client=service,
            training_client=training,
            model_name=model_name,
            tokenizer=tokenizer,
        )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;cls&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;model_name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;lora_rank&#x22;" type="&#x22;int&#x22;" value="&#x22;32&#x22;" />

    <PyParameter name="&#x22;renderer_name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;resume_state_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;init_weights_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;api_key_env&#x22;" type="&#x22;str&#x22;" value="&#x22;'TINKER_API_KEY'&#x22;" />

    <PyParameter name="&#x22;user_metadata&#x22;" type="&#x22;dict[str, str] | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;'TinkerBackend'&#x22;" />
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
        kwargs: dict[str, Any] = {"data": data, "loss_fn": loss_fn}
        if loss_fn_config:
            kwargs["loss_fn_config"] = loss_fn_config
        return _CoroFuture(self._training.forward_backward_async(**kwargs))
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
        return _CoroFuture(self._training.forward_backward_custom_async(
            data=data, loss_fn=loss_fn,
        ))
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
        return _CoroFuture(self._training.optim_step_async(adam))
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
        future = await self._training.save_weights_for_sampler_async(name)
        result = await future.result_async()
        return _result_path(result)
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
        future = await self._training.save_state_async(name)
        result = await future.result_async()
        return _result_path(result)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str&#x22;" />
</PyFunction>

<PyFunction name="&#x22;snapshot_sampling_client&#x22;" type="&#x22;(self, name=None) -> TinkerSamplingClient&#x22;">
  <PySourceCode>
    ```python
    async def snapshot_sampling_client(
        self, name: str | None = None
    ) -> TinkerSamplingClient:
        label = name or self._next_snapshot_label()
        sampler_path = await self.save_for_sampler(label)
        raw = self._service.create_sampling_client(
            base_model=self._model_name, model_path=sampler_path,
        )
        client = TinkerSamplingClient(raw, name=label)
        client.model_path = sampler_path  # harbor-backed evaluators re-sample from this
        return client
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.training.tinker_backend.TinkerSamplingClient&#x22;" />
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

<PyFunction name="&#x22;_next_snapshot_label&#x22;" type="&#x22;(self) -> str&#x22;">
  <PySourceCode>
    ```python
    def _next_snapshot_label(self) -> str:
        self._save_counter += 1
        return f"snap_{self._save_counter}"
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str&#x22;" />
</PyFunction>
