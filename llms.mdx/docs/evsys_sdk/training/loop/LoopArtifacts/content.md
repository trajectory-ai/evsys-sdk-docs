# LoopArtifacts (/docs/evsys_sdk/training/loop/LoopArtifacts)



Per-run filesystem + state pointers, used to populate `RunResult`.

## Attributes [#attributes]

<PyAttribute name="&#x22;run_dir&#x22;" type="&#x22;Path&#x22;" value="null">
  The output directory written to. Algorithms surface this as
  `RunResult.artifacts["run_dir"]` for downstream consumers
  (e.g. `TinkerInference.from_run_result`).
</PyAttribute>

<PyAttribute name="&#x22;manifest_path&#x22;" type="&#x22;Path&#x22;" value="null">
  Path to the `checkpoints.jsonl` the loop wrote.
</PyAttribute>

<PyAttribute name="&#x22;checkpoints&#x22;" type="&#x22;list[ManifestRow]&#x22;" value="null">
  Manifest rows the loop wrote, in order. The last one is the
  final sampler — the URI eval consumes.
</PyAttribute>

<PyAttribute name="&#x22;total_requested_steps&#x22;" type="&#x22;int&#x22;" value="null">
  The step horizon passed to :meth:`TrainingLoop.run` (`num_steps`) —
  NOT the number of steps actually executed. These differ when a callback
  early-stops the loop via `state.request_stop()`: this field still reads
  the requested ceiling. For "how many steps actually ran", read
  `state.step + 1` inside `on_train_end` (`state.step` is the last
  executed index).
</PyAttribute>

<PyAttribute name="&#x22;train_seconds&#x22;" type="&#x22;float&#x22;" value="null" />

## Functions [#functions]

<PyFunction name="&#x22;as_dict&#x22;" type="&#x22;(self) -> dict[str, str]&#x22;">
  Flatten to the `RunResult.artifacts` shape that downstream
  consumers (e.g. `TinkerInference.from_run_result`) read.

  <PySourceCode>
    ```python
    def as_dict(self) -> dict[str, str]:
        """Flatten to the ``RunResult.artifacts`` shape that downstream
        consumers (e.g. ``TinkerInference.from_run_result``) read."""
        out: dict[str, str] = {"run_dir": str(self.run_dir)}
        for row in self.checkpoints:
            if row.sampler_path:
                out[f"checkpoint-{row.name}"] = row.sampler_path
            # Full training-state path (weights + optimizer). Surfaced so
            # continual learning can chain a stage's final weights into the
            # next stage (see Experiment continual mode).
            if row.state_path:
                out[f"state-{row.name}"] = row.state_path
        return out
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;dict[str, str]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, run_dir, manifest_path, checkpoints, total_requested_steps, train_seconds) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_dir&#x22;" type="&#x22;Path&#x22;" value="null" />

    <PyParameter name="&#x22;manifest_path&#x22;" type="&#x22;Path&#x22;" value="null" />

    <PyParameter name="&#x22;checkpoints&#x22;" type="&#x22;list[ManifestRow]&#x22;" value="null" />

    <PyParameter name="&#x22;total_requested_steps&#x22;" type="&#x22;int&#x22;" value="null" />

    <PyParameter name="&#x22;train_seconds&#x22;" type="&#x22;float&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
