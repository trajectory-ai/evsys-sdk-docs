# HarborTaskAdapter (/docs/evsys_sdk/training/harbor_engine/HarborTaskAdapter)



Adapt our `HarborTask` rows (the `harbor_task` / RL + eval format) to
harbor task dirs + native `TaskConfig`\s — **scored** rollouts.

`to_harbor(output_dir)` writes one task dir per task (`instruction.md` +
a SHARED-mode `task.toml` + a dummy `tests/test.sh` that only satisfies
harbor's load check + the per-task `evsys_verifier.json` spec our host-side
:class:`~evsys_sdk.training.harbor_agents.EvsysVerifier` reads) and returns
the `TaskConfig`\s pointing at them. Requires an `InProcessVerifier`.
Used by `run_harbor_rollouts(..., outcome_reward=True)` (RL + benchmark eval).

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, tasks) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, tasks: Sequence[HarborTask]) -> None:
        self._tasks = list(tasks)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;tasks&#x22;" type="&#x22;Sequence[HarborTask]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;to_harbor&#x22;" type="&#x22;(self, output_dir) -> list[Any]&#x22;">
  <PySourceCode>
    ```python
    def to_harbor(self, output_dir: Path) -> list[Any]:
        from harbor.models.trial.config import TaskConfig

        configs: list[Any] = []
        for task in self._tasks:
            if not isinstance(task.verifier, InProcessVerifier):
                kind = getattr(task.verifier, "kind", type(task.verifier).__name__)
                raise RuntimeError(
                    f"harbor_engine: task {task.task_id!r} needs an in_process verifier "
                    f"for a scored rollout (got {kind!r}); only 'in_process' is supported."
                )
            name = _safe(task.task_id)
            dest = output_dir / name
            dest.mkdir(parents=True, exist_ok=True)
            (dest / "instruction.md").write_text(task.instruction)
            (dest / "task.toml").write_text(_ROLLOUT_TASK_TOML)
            tests = dest / "tests"
            tests.mkdir(exist_ok=True)
            (tests / "test.sh").write_text(_DUMMY_TEST_SH)  # dummy — satisfies load, never run
            v = task.verifier
            (dest / _VERIFIER_SPEC_FILE).write_text(json.dumps({
                "fn_name": v.fn_name,
                "expected": v.expected,
                "params": dict(getattr(v, "params", None) or {}),
            }))
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
