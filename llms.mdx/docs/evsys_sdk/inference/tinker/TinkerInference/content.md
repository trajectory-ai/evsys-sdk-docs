# TinkerInference (/docs/evsys_sdk/inference/tinker/TinkerInference)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'tinker'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;TinkerInferenceConfig&#x22;" />

<PyAttribute name="&#x22;model_name&#x22;" type="null" value="&#x22;model_name&#x22;" />

<PyAttribute name="&#x22;checkpoint_path&#x22;" type="null" value="&#x22;checkpoint_path&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, model_name, checkpoint_path=None, api_key_env='TINKER_API_KEY') -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(
        self,
        *,
        model_name: str,
        checkpoint_path: str | None = None,
        api_key_env: str = "TINKER_API_KEY",
    ) -> None:
        api_key = os.environ.get(api_key_env)
        if not api_key:
            raise RuntimeError(f"{api_key_env} not set in env")
        self.model_name = model_name
        self.checkpoint_path = checkpoint_path
        sc = tinker.ServiceClient()
        from tinker_cookbook.tokenizer_utils import get_tokenizer
        self._tokenizer = get_tokenizer(model_name)
        if checkpoint_path:
            self._client = sc.create_sampling_client(
                base_model=model_name, model_path=checkpoint_path
            )
        else:
            self._client = sc.create_sampling_client(base_model=model_name)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;model_name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;checkpoint_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;api_key_env&#x22;" type="&#x22;str&#x22;" value="&#x22;'TINKER_API_KEY'&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_submit&#x22;" type="&#x22;(self, prompt, max_tokens, temperature, stop)&#x22;">
  <PySourceCode>
    ```python
    def _submit(self, prompt: str, max_tokens: int, temperature: float, stop: list[str] | None):
        from tinker import ModelInput, SamplingParams

        ids = self._tokenizer.encode(prompt)
        model_input = ModelInput.from_ints(ids)
        params = SamplingParams(
            max_tokens=max_tokens,
            temperature=temperature,
            stop=stop or [],
        )
        return self._client.sample(prompt=model_input, sampling_params=params, num_samples=1)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;prompt&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;max_tokens&#x22;" type="&#x22;int&#x22;" value="null" />

    <PyParameter name="&#x22;temperature&#x22;" type="&#x22;float&#x22;" value="null" />

    <PyParameter name="&#x22;stop&#x22;" type="&#x22;list[str] | None&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="null" />
</PyFunction>

<PyFunction name="&#x22;_decode&#x22;" type="&#x22;(self, future) -> str&#x22;">
  <PySourceCode>
    ```python
    def _decode(self, future) -> str:
        result = future.result() if hasattr(future, "result") else future
        try:
            seq = result.sequences[0]
        except Exception:
            return ""
        token_ids = getattr(seq, "tokens", None) or getattr(seq, "token_ids", None)
        if token_ids:
            return self._tokenizer.decode(list(token_ids))
        return ""
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;future&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str&#x22;" />
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
        return self._decode(self._submit(prompt, max_tokens, temperature, stop))
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

<PyFunction name="&#x22;generate_batch&#x22;" type="&#x22;(self, *, prompts, max_tokens=256, temperature=0.0, stop=None) -> list[str]&#x22;">
  Submit all prompts concurrently, then collect results in order.

  <PySourceCode>
    ```python
    def generate_batch(
        self,
        *,
        prompts: list[str],
        max_tokens: int = 256,
        temperature: float = 0.0,
        stop: list[str] | None = None,
    ) -> list[str]:
        """Submit all prompts concurrently, then collect results in order."""
        futures = [self._submit(p, max_tokens, temperature, stop) for p in prompts]
        return [self._decode(f) for f in futures]
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;prompts&#x22;" type="&#x22;list[str]&#x22;" value="null" />

    <PyParameter name="&#x22;max_tokens&#x22;" type="&#x22;int&#x22;" value="&#x22;256&#x22;" />

    <PyParameter name="&#x22;temperature&#x22;" type="&#x22;float&#x22;" value="&#x22;0.0&#x22;" />

    <PyParameter name="&#x22;stop&#x22;" type="&#x22;list[str] | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;list[str]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;from_run_result&#x22;" type="&#x22;(cls, run_result, run_cfg, *, label='final') -> 'TinkerInference'&#x22;">
  Build a TinkerInference pointing at the run's final sampler checkpoint.

  Reads `run_result.artifacts["run_dir"]`, locates `checkpoints.jsonl`
  via :func:`find_manifest`, picks the checkpoint matching `label`
  (default `"final"`), and instantiates with `run_cfg.model.name` +
  that sampler URI. Raises a clear `RuntimeError` for each missing
  piece so callers can diagnose without spelunking.

  <PySourceCode>
    ```python
    @classmethod
    def from_run_result(cls, run_result: Any, run_cfg: Any, *,
                        label: str = "final") -> "TinkerInference":
        """Build a TinkerInference pointing at the run's final sampler checkpoint.

        Reads ``run_result.artifacts["run_dir"]``, locates ``checkpoints.jsonl``
        via :func:`find_manifest`, picks the checkpoint matching ``label``
        (default ``"final"``), and instantiates with ``run_cfg.model.name`` +
        that sampler URI. Raises a clear ``RuntimeError`` for each missing
        piece so callers can diagnose without spelunking.
        """
        artifacts = getattr(run_result, "artifacts", None) or {}
        run_dir = artifacts.get("run_dir")
        if not run_dir:
            raise RuntimeError("run_result.artifacts has no 'run_dir'")
        manifest = find_manifest(run_dir)
        if manifest is None:
            raise RuntimeError(f"no checkpoints.jsonl under {run_dir}")
        chosen = Checkpoint.pick_final(read_manifest(manifest))
        if chosen is None or not chosen.sampler_path:
            raise RuntimeError(
                f"no usable sampler checkpoint at {label!r} in {manifest}"
            )
        return cls(model_name=run_cfg.model.name, checkpoint_path=chosen.sampler_path)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;cls&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_result&#x22;" type="&#x22;Any&#x22;" value="null" />

    <PyParameter name="&#x22;run_cfg&#x22;" type="&#x22;Any&#x22;" value="null" />

    <PyParameter name="&#x22;label&#x22;" type="&#x22;str&#x22;" value="&#x22;'final'&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;'TinkerInference'&#x22;" />
</PyFunction>
