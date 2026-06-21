# Benchmark (/docs/evsys_sdk/benchmark/Benchmark)



A harbor-format eval suite loaded into memory.

Two builders:

* `from_dir(path)`   — `data/benchmark/\<name>/\{tasks.jsonl,metadata.yaml\}`
* `from_iterable(name, rows, metadata=...)` — for tests / programmatic.

`score(client)` runs inference + verification and returns a `BenchmarkScore`.

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

<PyAttribute name="&#x22;tasks&#x22;" type="&#x22;list[HarborTask]&#x22;" value="null" />

<PyAttribute name="&#x22;metadata&#x22;" type="&#x22;dict&#x22;" value="&#x22;field(default_factory=dict)&#x22;" />

<PyAttribute name="&#x22;root&#x22;" type="&#x22;Path | None&#x22;" value="&#x22;None&#x22;">
  Filesystem dir the benchmark was loaded from (None if in-memory).
</PyAttribute>

## Functions [#functions]

<PyFunction name="&#x22;from_dir&#x22;" type="&#x22;(cls, path) -> Benchmark&#x22;">
  <PySourceCode>
    ```python
    @classmethod
    def from_dir(cls, path: str | Path) -> Benchmark:
        root = Path(path)
        if not root.is_dir():
            raise FileNotFoundError(f"benchmark dir not found: {root}")

        tasks_path = root / "tasks.jsonl"
        if not tasks_path.is_file():
            raise FileNotFoundError(f"benchmark missing tasks.jsonl: {tasks_path}")

        tasks: list[HarborTask] = []
        for lineno, line in enumerate(tasks_path.read_text().splitlines(), 1):
            line = line.strip()
            if not line:
                continue
            try:
                row = json.loads(line)
            except json.JSONDecodeError as e:
                raise ValueError(f"{tasks_path}:{lineno}: malformed json: {e}") from e
            try:
                tasks.append(harbor_task_from_dict(row))
            except (KeyError, ValueError) as e:
                raise ValueError(f"{tasks_path}:{lineno}: {e}") from e

        metadata = _read_metadata_yaml(root / "metadata.yaml")
        name = str(metadata.get("name") or root.name)
        return cls(name=name, tasks=tasks, metadata=metadata, root=root)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;cls&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;path&#x22;" type="&#x22;str | Path&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.benchmark.Benchmark&#x22;" />
</PyFunction>

<PyFunction name="&#x22;from_iterable&#x22;" type="&#x22;(cls, name, rows, *, metadata=None) -> Benchmark&#x22;">
  <PySourceCode>
    ```python
    @classmethod
    def from_iterable(
        cls,
        name: str,
        rows: list[dict] | list[HarborTask],
        *,
        metadata: dict | None = None,
    ) -> Benchmark:
        tasks: list[HarborTask] = []
        for row in rows:
            if isinstance(row, HarborTask):
                tasks.append(row)
            else:
                tasks.append(harbor_task_from_dict(row))
        return cls(name=name, tasks=tasks, metadata=dict(metadata or {}), root=None)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;cls&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;rows&#x22;" type="&#x22;list[dict] | list[HarborTask]&#x22;" value="null" />

    <PyParameter name="&#x22;metadata&#x22;" type="&#x22;dict | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.benchmark.Benchmark&#x22;" />
</PyFunction>

<PyFunction name="&#x22;load&#x22;" type="&#x22;(cls, spec, *, store=None) -> Benchmark | None&#x22;">
  Resolve a benchmark spec `\{path | id | name\}` to a `Benchmark`.

  The single resolver shared by the experiment config, the standalone
  `run_benchmark`, and the CLI — so all accept the same references:

  * `path` → local harbor dir (delegates to `from_dir`); offline / dev.
  * `id`   → dashboard benchmark id, pulled into the local `.evsys/`.
  * `name` → resolved to the latest version's id, then pulled.

  Returns `None` when the spec carries none of those. `store` is
  needed only for the id / name paths.

  <PySourceCode>
    ```python
    @classmethod
    def load(cls, spec: dict[str, Any], *, store: Any = None) -> Benchmark | None:
        """Resolve a benchmark spec ``{path | id | name}`` to a ``Benchmark``.

        The single resolver shared by the experiment config, the standalone
        ``run_benchmark``, and the CLI — so all accept the same references:

        * ``path`` → local harbor dir (delegates to ``from_dir``); offline / dev.
        * ``id``   → dashboard benchmark id, pulled into the local ``.evsys/``.
        * ``name`` → resolved to the latest version's id, then pulled.

        Returns ``None`` when the spec carries none of those. ``store`` is
        needed only for the id / name paths.
        """
        path = spec.get("path")
        if path:
            return cls.from_dir(path)
        bid, dashboard_name = spec.get("id"), spec.get("name")
        if not (bid or dashboard_name):
            return None
        from .workspace import Workspace, read_jsonl_rows

        ws = Workspace(store) if store is not None else Workspace()
        resolved = str(bid) if bid else ws.benchmark_id_for_name(str(dashboard_name))
        mat = ws.pull_benchmark(resolved)
        tasks = [harbor_task_from_dict(r) for r in read_jsonl_rows(mat.path)]
        return cls.from_iterable(str(dashboard_name or bid or "benchmark"), tasks)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;cls&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;spec&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />

    <PyParameter name="&#x22;store&#x22;" type="&#x22;Any&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.benchmark.Benchmark | None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;score&#x22;" type="&#x22;(self, client, *, max_tokens=512, temperature=0.0, stop=None, prompt_builder=None, breakdown_keys=None, limit=None, metrics=None, num_samples=1) -> BenchmarkScore&#x22;">
  Run each task through `client` and score the completion.

  Sequential — wrap in a thread/process pool externally if you need
  concurrency. (Most local clients are GPU-bound and don't benefit.)

  `prompt_builder(task) -> str` lets callers shape the model input;
  default is `task.instruction` verbatim.

  `breakdown_keys` are dotted attribute paths into `task.metadata`. Each
  key produces `\{value -> \{n, mean_reward, pass_rate\}\}` in the result.

  `limit` caps how many tasks are scored — the first `limit` in
  `self.tasks` (deterministic, in benchmark order). Useful for fast
  smoke-runs on large benchmarks. `None` means score everything.

  <PySourceCode>
    ```python
    def score(
        self,
        client: InferenceClient,
        *,
        max_tokens: int = 512,
        temperature: float = 0.0,
        stop: list[str] | None = None,
        prompt_builder: "callable | None" = None,
        breakdown_keys: list[str] | None = None,
        limit: int | None = None,
        metrics: list[str] | None = None,
        num_samples: int = 1,
    ) -> BenchmarkScore:
        """Run each task through `client` and score the completion.

        Sequential — wrap in a thread/process pool externally if you need
        concurrency. (Most local clients are GPU-bound and don't benefit.)

        `prompt_builder(task) -> str` lets callers shape the model input;
        default is `task.instruction` verbatim.

        `breakdown_keys` are dotted attribute paths into `task.metadata`. Each
        key produces `{value -> {n, mean_reward, pass_rate}}` in the result.

        `limit` caps how many tasks are scored — the first `limit` in
        `self.tasks` (deterministic, in benchmark order). Useful for fast
        smoke-runs on large benchmarks. ``None`` means score everything.
        """
        if breakdown_keys is None:
            breakdown_keys = []
        tasks_iter = self.tasks if limit is None else self.tasks[: max(0, int(limit))]
        n_samples = max(1, int(num_samples))

        per_task: list[BenchmarkTaskResult] = []
        task_rewards: list[list[float]] = []
        for task in tasks_iter:
            prompt = prompt_builder(task) if prompt_builder else task.instruction
            sample_rewards: list[float] = []
            last_completion, last_expected = "", None
            for _ in range(n_samples):
                completion = client.generate(
                    prompt=prompt,
                    max_tokens=max_tokens,
                    temperature=temperature,
                    stop=stop,
                )
                reward, expected = _score_task(task, completion)
                sample_rewards.append(reward)
                last_completion, last_expected = completion, expected
            task_rewards.append(sample_rewards)
            per_task.append(
                BenchmarkTaskResult(
                    task_id=task.task_id,
                    instruction=task.instruction,
                    model_output=last_completion,
                    expected=last_expected,
                    # Per-task mean reward — drives the breakdown buckets.
                    reward=sum(sample_rewards) / len(sample_rewards),
                    metadata=dict(task.metadata),
                )
            )

        n = len(per_task)
        names = list(metrics) if metrics else ["mean_reward", "pass_rate"]
        score_metrics: dict[str, float] = {"n_tasks": float(n)}
        for name in names:
            try:
                score_metrics[name] = float(get_metric(name)().compute(task_rewards))
            except Exception:
                logger.warning("benchmark metric %r failed; skipping", name, exc_info=True)
        metrics = score_metrics
        breakdowns = _compute_breakdowns(per_task, breakdown_keys)
        return BenchmarkScore(metrics=metrics, per_task=per_task, breakdowns=breakdowns)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;client&#x22;" type="&#x22;InferenceClient&#x22;" value="null" />

    <PyParameter name="&#x22;max_tokens&#x22;" type="&#x22;int&#x22;" value="&#x22;512&#x22;" />

    <PyParameter name="&#x22;temperature&#x22;" type="&#x22;float&#x22;" value="&#x22;0.0&#x22;" />

    <PyParameter name="&#x22;stop&#x22;" type="&#x22;list[str] | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;prompt_builder&#x22;" type="&#x22;'callable | None'&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;breakdown_keys&#x22;" type="&#x22;list[str] | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;limit&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;list[str] | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;num_samples&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.benchmark.BenchmarkScore&#x22;" />
</PyFunction>

<PyFunction name="&#x22;score_via_harbor&#x22;" type="&#x22;(self, *, model_name, model_path=None, model_client='tinker', workspace_dir, renderer_name=None, num_samples=1, max_tokens=512, temperature=0.0, system_prompt=None, limit=None, breakdown_keys=None, metrics=None, n_concurrent=8, agent_import_path=None, max_retries=2, _job_factory=None) -> BenchmarkScore&#x22;">
  Score this benchmark through harbor's rollout engine — the harbor
  counterpart of :meth:`score`, returning the same :class:`BenchmarkScore`.

  Each task is rolled out `num_samples` times (one verifier reward per
  sample); `metrics` (registry names like `pass@3`) reduce the per-task
  sample rewards, and `time/tokens/cost_per_task` come from harbor usage.
  `model_client` is `"tinker"` (on-policy checkpoint, needs
  `model_path`) or `"litellm"` (closed/API model; `model_name` a
  litellm string). The result's :attr:`BenchmarkScore.rollouts` carries the
  raw per-(task, sample) rollouts so callers can upload eval predictions
  without re-running.

  <PySourceCode>
    ```python
    async def score_via_harbor(
        self,
        *,
        model_name: str,
        model_path: str | None = None,
        model_client: str = "tinker",
        workspace_dir: Path,
        renderer_name: str | None = None,
        num_samples: int = 1,
        max_tokens: int = 512,
        temperature: float = 0.0,
        system_prompt: str | None = None,
        limit: int | None = None,
        breakdown_keys: list[str] | None = None,
        metrics: list[str] | None = None,
        n_concurrent: int = 8,
        agent_import_path: str | None = None,
        max_retries: int = 2,
        _job_factory: Any | None = None,
    ) -> BenchmarkScore:
        """Score this benchmark through harbor's rollout engine — the harbor
        counterpart of :meth:`score`, returning the same :class:`BenchmarkScore`.

        Each task is rolled out ``num_samples`` times (one verifier reward per
        sample); ``metrics`` (registry names like ``pass@3``) reduce the per-task
        sample rewards, and ``time/tokens/cost_per_task`` come from harbor usage.
        ``model_client`` is ``"tinker"`` (on-policy checkpoint, needs
        ``model_path``) or ``"litellm"`` (closed/API model; ``model_name`` a
        litellm string). The result's :attr:`BenchmarkScore.rollouts` carries the
        raw per-(task, sample) rollouts so callers can upload eval predictions
        without re-running.
        """
        from .training.harbor_engine import run_harbor_rollouts
        from .training.harbor_eval import eval_metrics

        tasks = self.tasks if limit is None else self.tasks[: max(0, int(limit))]
        groups = await run_harbor_rollouts(
            tasks,                       # HarborTasks → outcome_reward=True (default), scored
            model_name=model_name,
            model_path=model_path,
            model_client=model_client,
            workspace_dir=workspace_dir,
            renderer_name=renderer_name,
            num_samples=num_samples,
            max_tokens=max_tokens,
            temperature=temperature,
            system_prompt=system_prompt,
            n_concurrent=n_concurrent,
            agent_import_path=agent_import_path,
            max_retries=max_retries,
            _job_factory=_job_factory,
        )
        score_metrics = eval_metrics(groups, metrics=list(metrics) if metrics else None)
        per_task: list[BenchmarkTaskResult] = []
        for task, group in zip(tasks, groups):
            rewards = list(group.rewards)
            per_task.append(
                BenchmarkTaskResult(
                    task_id=task.task_id,
                    instruction=task.instruction,
                    # harbor harvests token ids, not completion text.
                    model_output="",
                    expected=getattr(task.verifier, "expected", None),
                    # Per-task mean reward — drives the breakdown buckets.
                    reward=(sum(rewards) / len(rewards)) if rewards else 0.0,
                    metadata=dict(task.metadata),
                )
            )
        breakdowns = _compute_breakdowns(per_task, breakdown_keys or [])
        return BenchmarkScore(
            metrics=score_metrics,
            per_task=per_task,
            breakdowns=breakdowns,
            rollouts=list(groups),
        )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;model_name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;model_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;model_client&#x22;" type="&#x22;str&#x22;" value="&#x22;'tinker'&#x22;" />

    <PyParameter name="&#x22;workspace_dir&#x22;" type="&#x22;Path&#x22;" value="null" />

    <PyParameter name="&#x22;renderer_name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;num_samples&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />

    <PyParameter name="&#x22;max_tokens&#x22;" type="&#x22;int&#x22;" value="&#x22;512&#x22;" />

    <PyParameter name="&#x22;temperature&#x22;" type="&#x22;float&#x22;" value="&#x22;0.0&#x22;" />

    <PyParameter name="&#x22;system_prompt&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;limit&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;breakdown_keys&#x22;" type="&#x22;list[str] | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;list[str] | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;n_concurrent&#x22;" type="&#x22;int&#x22;" value="&#x22;8&#x22;" />

    <PyParameter name="&#x22;agent_import_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;max_retries&#x22;" type="&#x22;int&#x22;" value="&#x22;2&#x22;" />

    <PyParameter name="&#x22;_job_factory&#x22;" type="&#x22;Any | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.benchmark.BenchmarkScore&#x22;" />
</PyFunction>

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, name, tasks, metadata=dict(), root=None) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;tasks&#x22;" type="&#x22;list[HarborTask]&#x22;" value="null" />

    <PyParameter name="&#x22;metadata&#x22;" type="&#x22;dict&#x22;" value="&#x22;dict()&#x22;" />

    <PyParameter name="&#x22;root&#x22;" type="&#x22;Path | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
