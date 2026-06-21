# BasicLoopAgent (/docs/evsys_sdk/training/harbor_agents/BasicLoopAgent)



Drive `Chat(TinkerLLM(model_path))` and record the rollout: token-level
`rollout_details` + the completion text on the `AgentContext`, and the
completion written to the agent dir for the host-side verifier to score.

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, model_name, model_path=None, renderer_name=None, max_tokens=512, temperature=1.0, max_turns=1, system_prompt=None, model_client='tinker', api_base=None, **kw) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(
        self,
        *,
        model_name: str,
        model_path: str | None = None,
        renderer_name: str | None = None,
        max_tokens: int = 512,
        temperature: float = 1.0,
        max_turns: int = 1,
        system_prompt: str | None = None,
        model_client: str = "tinker",
        api_base: str | None = None,
        **kw: Any,
    ) -> None:
        super().__init__(**kw)
        self._model_name = model_name
        self._model_path = model_path
        self._renderer_name = renderer_name
        self._max_tokens = max_tokens
        self._temperature = temperature
        self._max_turns = max_turns
        self._system_prompt = system_prompt
        self._model_client = model_client
        self._api_base = api_base
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;model_name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;model_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;renderer_name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;max_tokens&#x22;" type="&#x22;int&#x22;" value="&#x22;512&#x22;" />

    <PyParameter name="&#x22;temperature&#x22;" type="&#x22;float&#x22;" value="&#x22;1.0&#x22;" />

    <PyParameter name="&#x22;max_turns&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />

    <PyParameter name="&#x22;system_prompt&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;model_client&#x22;" type="&#x22;str&#x22;" value="&#x22;'tinker'&#x22;" />

    <PyParameter name="&#x22;api_base&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;kw&#x22;" type="&#x22;Any&#x22;" value="&#x22;{}&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;name&#x22;" type="&#x22;() -> str&#x22;">
  <PySourceCode>
    ```python
    @staticmethod
    def name() -> str:
        return "evsys-basic-loop"
    ```
  </PySourceCode>

  <PyFunctionReturn type="&#x22;str&#x22;" />
</PyFunction>

<PyFunction name="&#x22;version&#x22;" type="&#x22;(self) -> str | None&#x22;">
  <PySourceCode>
    ```python
    def version(self) -> str | None:
        return "1.0.0"
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str | None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;setup&#x22;" type="&#x22;(self, environment) -> None&#x22;">
  <PySourceCode>
    ```python
    async def setup(self, environment: BaseEnvironment) -> None:
        return None
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;environment&#x22;" type="&#x22;BaseEnvironment&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_build_llm&#x22;" type="&#x22;(self) -> Any&#x22;">
  The harbor sampler for this rollout. `model_client` picks it:
  `"tinker"` → on-policy `TinkerLLM` (needs `model_path`);
  `"litellm"` → harbor's litellm LLM for any provider (`model_name` a
  litellm string, e.g. `"anthropic/claude-opus-4-1"`; keys from the
  provider env vars).

  `collect_rollout_details` is ON for tinker (we want token-level
  rollouts for training) but OFF for litellm: it makes harbor request
  `logprobs` + `extra_body.return_token_ids`, which closed APIs
  (Anthropic, OpenAI) reject with a 400. API-model benchmarking only needs
  the completion + reward + usage (cost/tokens), not token ids — those are
  still captured from the response. See `_trial_to_trajectory`.

  <PySourceCode>
    ```python
    def _build_llm(self) -> Any:
        """The harbor sampler for this rollout. ``model_client`` picks it:
        ``"tinker"`` → on-policy ``TinkerLLM`` (needs ``model_path``);
        ``"litellm"`` → harbor's litellm LLM for any provider (``model_name`` a
        litellm string, e.g. ``"anthropic/claude-opus-4-1"``; keys from the
        provider env vars).

        ``collect_rollout_details`` is ON for tinker (we want token-level
        rollouts for training) but OFF for litellm: it makes harbor request
        ``logprobs`` + ``extra_body.return_token_ids``, which closed APIs
        (Anthropic, OpenAI) reject with a 400. API-model benchmarking only needs
        the completion + reward + usage (cost/tokens), not token ids — those are
        still captured from the response. See ``_trial_to_trajectory``."""
        if self._model_client == "litellm":
            from harbor.llms.lite_llm import LiteLLM  # lazy: tinker rollouts skip litellm

            return LiteLLM(
                model_name=self._model_name,
                temperature=self._temperature,
                api_base=self._api_base,
                collect_rollout_details=False,  # APIs reject logprobs/return_token_ids
            )
        return TinkerLLM(
            model_name=self._model_name,
            model_path=self._model_path,
            renderer_name=self._renderer_name,
            collect_rollout_details=True,
            max_tokens=self._max_tokens,
            temperature=self._temperature,
        )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_cache_key&#x22;" type="&#x22;(self) -> tuple&#x22;">
  <PySourceCode>
    ```python
    def _cache_key(self) -> tuple:
        return (
            self._model_client,
            self._model_name,
            self._model_path,
            self._renderer_name,
            self._max_tokens,
            self._temperature,
            self._api_base,
        )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;tuple&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_shared_llm&#x22;" type="&#x22;(self) -> Any&#x22;">
  The cached LLM for this rollout's (loop, config) — built once per
  harbor job and reused by every trial (see the module cache note). The
  first build is warmed under a per-loop lock so concurrent trials share
  ONE sampling client instead of each creating their own.

  <PySourceCode>
    ```python
    async def _shared_llm(self) -> Any:
        """The cached LLM for this rollout's (loop, config) — built once per
        harbor job and reused by every trial (see the module cache note). The
        first build is warmed under a per-loop lock so concurrent trials share
        ONE sampling client instead of each creating their own."""
        loop = asyncio.get_running_loop()
        per_loop = _LLM_CACHE.setdefault(loop, {})
        lock = _LLM_LOCKS.setdefault(loop, asyncio.Lock())
        key = self._cache_key()
        async with lock:
            llm = per_loop.get(key)
            if llm is None:
                llm = self._build_llm()
                ensure = getattr(llm, "_ensure_client", None)
                if ensure is not None:
                    await ensure()  # create the sampling client once, under lock
                per_loop[key] = llm
            return llm
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
</PyFunction>

<PyFunction name="&#x22;run&#x22;" type="&#x22;(self, instruction, environment, context) -> None&#x22;">
  <PySourceCode>
    ```python
    async def run(
        self,
        instruction: str,
        environment: BaseEnvironment,
        context: AgentContext,
    ) -> None:
        chat = Chat(await self._shared_llm())
        if self._system_prompt:
            chat.messages.append({"role": "system", "content": self._system_prompt})
        resp = await chat.chat(instruction)
        context.rollout_details = chat.rollout_details   # token-level (tinker); empty for API models
        # Propagate usage/cost onto the AgentContext so harbor records it on the
        # trial. For API models (no token ids) this is the ONLY token/cost source;
        # _trial_usage reads these back into the eval metrics.
        context.n_input_tokens = chat.total_input_tokens
        context.n_output_tokens = chat.total_output_tokens
        context.n_cache_tokens = chat.total_cache_tokens
        context.cost_usd = chat.total_cost
        # Write the completion to the agent dir so the host-side EvsysVerifier
        # (run by harbor) can read it (self.logs_dir == trial_paths.agent_dir).
        logs_dir = getattr(self, "logs_dir", None)
        if logs_dir is not None:
            Path(logs_dir).mkdir(parents=True, exist_ok=True)
            (Path(logs_dir) / _COMPLETION_FILE).write_text(resp.content or "")
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;instruction&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;environment&#x22;" type="&#x22;BaseEnvironment&#x22;" value="null" />

    <PyParameter name="&#x22;context&#x22;" type="&#x22;AgentContext&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
