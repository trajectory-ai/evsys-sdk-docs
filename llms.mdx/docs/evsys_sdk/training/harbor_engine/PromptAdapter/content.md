# PromptAdapter (/docs/evsys_sdk/training/harbor_engine/PromptAdapter)



Adapt raw prompts (the generation / "prompt" format) to harbor task dirs +
native `TaskConfig`\s — **generation-only** rollouts (no verifier, no
reward). Used by SDFT student rollouts.

`to_harbor(output_dir)` writes `instruction.md` + a verifier-less
`task.toml` (`environment_mode="separate"` skips the test.sh load check)
per prompt and returns the `TaskConfig`\s. Used by
`run_harbor_rollouts(..., outcome_reward=False)` (SDFT student rollouts).

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, prompts) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, prompts: Sequence[str]) -> None:
        self._prompts = list(prompts)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;prompts&#x22;" type="&#x22;Sequence[str]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;to_harbor&#x22;" type="&#x22;(self, output_dir) -> list[Any]&#x22;">
  <PySourceCode>
    ```python
    def to_harbor(self, output_dir: Path) -> list[Any]:
        from harbor.models.trial.config import TaskConfig

        configs: list[Any] = []
        for i, prompt in enumerate(self._prompts):
            name = _safe(f"gen_{i}")
            dest = output_dir / name
            dest.mkdir(parents=True, exist_ok=True)
            (dest / "instruction.md").write_text(prompt)
            (dest / "task.toml").write_text(_GENERATION_TASK_TOML)
            configs.append(TaskConfig(path=dest))
        return configs
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;output_dir&#x22;" type="&#x22;Path&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;list[typing.Any]&#x22;" />
</PyFunction>
