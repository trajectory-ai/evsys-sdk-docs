# benchmark (/docs/evsys_sdk/benchmark)



Benchmark — load a harbor-format eval suite and score a model against it.

A benchmark on disk is a directory:

data/benchmark/\<name>/
tasks.jsonl       # required — one HarborTask per line
metadata.yaml     # optional — description, version, source, splits, ...
images/           # optional — referenced by relative path from tasks
raw/              # optional — pre-harbor source for traceability

`Benchmark.from_dir(path)` loads the suite. `bench.score(client)` runs each
task's `instruction` through an `InferenceClient`, then scores the completion
via the SDK's in-process verifier-fn registry (`verifiers/fns.py`). The result
collects per-task rows, top-level aggregates (`mean_reward`, `pass_rate`,
`n_tasks`), and optional breakdown buckets (e.g. per-toolkit pass rate) keyed
by an attribute path into `metadata`.

E2B and LLM-judge verifiers are recognized but not executed here — they need
network / sandboxes and live behind their own runners. Tasks carrying those
verifier kinds raise a clear error so callers don't silently mis-score.

<PyAttribute name="&#x22;logger&#x22;" type="null" value="&#x22;logging.getLogger(__name__)&#x22;" />

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['Benchmark', 'BenchmarkScore', 'BenchmarkTaskResult']&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;BenchmarkTaskResult&#x22;" href="&#x22;/docs/evsys_sdk/benchmark/BenchmarkTaskResult&#x22;" />

      <Card title="&#x22;BenchmarkScore&#x22;" href="&#x22;/docs/evsys_sdk/benchmark/BenchmarkScore&#x22;" />

      <Card title="&#x22;Benchmark&#x22;" href="&#x22;/docs/evsys_sdk/benchmark/Benchmark&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;_score_task&#x22;" type="&#x22;(task, completion) -> tuple[float, Any]&#x22;">
      <PySourceCode>
        ```python
        def _score_task(task: HarborTask, completion: str) -> tuple[float, Any]:
            verifier = task.verifier
            if isinstance(verifier, InProcessVerifier):
                fn = verifier_fns.get(verifier.fn_name)
                reward = float(fn(completion, verifier.expected, dict(verifier.params or {})))
                return reward, verifier.expected
            if isinstance(verifier, E2BVerifier):
                raise NotImplementedError(
                    f"task {task.task_id!r} uses E2BVerifier; run it through an E2B-aware "
                    "scorer rather than Benchmark.score"
                )
            if isinstance(verifier, LLMJudgeVerifier):
                raise NotImplementedError(
                    f"task {task.task_id!r} uses LLMJudgeVerifier; run it through an "
                    "LLM-judge scorer rather than Benchmark.score"
                )
            raise TypeError(f"task {task.task_id!r} has unknown verifier type: {type(verifier).__name__}")
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;task&#x22;" type="&#x22;HarborTask&#x22;" value="null" />

        <PyParameter name="&#x22;completion&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;tuple[float, typing.Any]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_read_metadata_yaml&#x22;" type="&#x22;(path) -> dict&#x22;">
      <PySourceCode>
        ```python
        def _read_metadata_yaml(path: Path) -> dict:
            if not path.is_file():
                return {}
            import yaml  # local — pyyaml is a required dep

            data = yaml.safe_load(path.read_text()) or {}
            if not isinstance(data, dict):
                raise ValueError(f"{path}: metadata.yaml must be a mapping at the top level")
            return data
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;path&#x22;" type="&#x22;Path&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;dict&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_compute_breakdowns&#x22;" type="&#x22;(per_task, breakdown_keys) -> dict[str, dict[str, dict[str, float]]]&#x22;">
      Bucket per-task rewards by each dotted `metadata` key →
      `\{key: \{value: \{n, mean_reward, pass_rate\}\}\}`. Shared by the in-process
      and harbor scoring paths.

      <PySourceCode>
        ```python
        def _compute_breakdowns(
            per_task: list[BenchmarkTaskResult], breakdown_keys: list[str],
        ) -> dict[str, dict[str, dict[str, float]]]:
            """Bucket per-task rewards by each dotted ``metadata`` key →
            ``{key: {value: {n, mean_reward, pass_rate}}}``. Shared by the in-process
            and harbor scoring paths."""
            breakdowns: dict[str, dict[str, dict[str, float]]] = {}
            for key in breakdown_keys:
                buckets: dict[str, list[float]] = {}
                for r in per_task:
                    bucket = str(_dotted_get(r.metadata, key, "__missing__"))
                    buckets.setdefault(bucket, []).append(r.reward)
                breakdowns[key] = {
                    bucket: {
                        "n": float(len(rewards)),
                        "mean_reward": sum(rewards) / len(rewards),
                        "pass_rate": sum(1 for x in rewards if x >= 1.0) / len(rewards),
                    }
                    for bucket, rewards in buckets.items()
                }
            return breakdowns
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;per_task&#x22;" type="&#x22;list[BenchmarkTaskResult]&#x22;" value="null" />

        <PyParameter name="&#x22;breakdown_keys&#x22;" type="&#x22;list[str]&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;dict[str, dict[str, dict[str, float]]]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_dotted_get&#x22;" type="&#x22;(d, dotted_key, default) -> Any&#x22;">
      <PySourceCode>
        ```python
        def _dotted_get(d: dict, dotted_key: str, default: Any) -> Any:
            cur: Any = d
            for part in dotted_key.split("."):
                if not isinstance(cur, dict) or part not in cur:
                    return default
                cur = cur[part]
            return cur
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;d&#x22;" type="&#x22;dict&#x22;" value="null" />

        <PyParameter name="&#x22;dotted_key&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;default&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
