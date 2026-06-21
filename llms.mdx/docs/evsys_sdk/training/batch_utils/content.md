# batch_utils (/docs/evsys_sdk/training/batch_utils)



Small shared helpers for turning backend forward-backward / sampling
outputs into plain Python, used by the SFT / SDFT algorithms' `step_metrics`
and batch construction.

Kept backend-agnostic: `MockBackend` emits Python lists; the real
`TinkerBackend` emits `tinker.TensorData` (with `.to_torch()`) and
tinker `SamplingResponse` objects. These helpers normalize both.

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['coerce_floats', 'extract_weights', 'extract_completion_tokens_from_response']&#x22;" />

<Tabs items="[&#x22;Functions&#x22;]">
  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;coerce_floats&#x22;" type="&#x22;(value) -> list[float] | None&#x22;">
      Best-effort: turn a TensorData / list / torch.Tensor into list\[float].

      Returns `None` when the value can't be coerced (so callers can skip it).

      <PySourceCode>
        ```python
        def coerce_floats(value: Any) -> list[float] | None:
            """Best-effort: turn a TensorData / list / torch.Tensor into list[float].

            Returns ``None`` when the value can't be coerced (so callers can skip it).
            """
            if value is None:
                return None
            if isinstance(value, list):
                return _flatten_floats(value)
            if hasattr(value, "to_torch"):
                # reshape(-1) flattens any rank (real tinker can return 2D logprobs for
                # the SDFT top-K CE datums; SFT's are 1D and pass through unchanged).
                return [float(v) for v in value.to_torch().reshape(-1).tolist()]
            if hasattr(value, "tolist"):
                return _flatten_floats(value.tolist())
            return None
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;value&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;list[float] | None&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_flatten_floats&#x22;" type="&#x22;(seq) -> list[float] | None&#x22;">
      Flatten an arbitrarily-nested list of numbers to `list[float]`.
      Returns `None` if a leaf isn't coercible to float.

      <PySourceCode>
        ```python
        def _flatten_floats(seq: Any) -> list[float] | None:
            """Flatten an arbitrarily-nested list of numbers to ``list[float]``.
            Returns ``None`` if a leaf isn't coercible to float."""
            flat: list[Any] = []

            def _walk(x: Any) -> None:
                if isinstance(x, (list, tuple)):
                    for e in x:
                        _walk(e)
                else:
                    flat.append(x)

            _walk(seq)
            try:
                return [float(v) for v in flat]
            except (TypeError, ValueError):
                return None
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;seq&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;list[float] | None&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;extract_weights&#x22;" type="&#x22;(datum) -> Any&#x22;">
      Pull the per-position weight mask out of a Datum's loss\_fn\_inputs.

      <PySourceCode>
        ```python
        def extract_weights(datum: tinker.Datum) -> Any:
            """Pull the per-position weight mask out of a Datum's loss_fn_inputs."""
            inputs = getattr(datum, "loss_fn_inputs", None)
            if not inputs:
                return None
            return inputs.get("weights")
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;datum&#x22;" type="&#x22;tinker.Datum&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;extract_completion_tokens_from_response&#x22;" type="&#x22;(response) -> list[int]&#x22;">
      Pull the token-id list out of a tinker SamplingResponse-shape object.

      Real tinker exposes `.sequences[0].tokens`; MockSamplingClient does the
      same; either way we get a list of ints back.

      <PySourceCode>
        ```python
        def extract_completion_tokens_from_response(response: Any) -> list[int]:
            """Pull the token-id list out of a tinker SamplingResponse-shape object.

            Real tinker exposes ``.sequences[0].tokens``; MockSamplingClient does the
            same; either way we get a list of ints back.
            """
            seqs = getattr(response, "sequences", None)
            if not seqs:
                return []
            first = seqs[0]
            tokens = getattr(first, "tokens", None) or getattr(first, "token_ids", None)
            if not tokens:
                return []
            return [int(t) for t in tokens]
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;response&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;list[int]&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
