# harbor_engine (/docs/evsys_sdk/training/harbor_engine)



Harbor rollout runner — hands rollouts to harbor's `Job` engine (0.13.2).

Import-safe **without** the `harbor` package: all harbor imports are lazy
(inside the runners), and the agent / environment classes are referenced only by
**string import path** (they live in :mod:`evsys_sdk.training.harbor_agents`,
which harbor loads at trial runtime). So `rl` / `sdft` can import this
module, and tests can mock the runners, with no `harbor` install.

Flow (harbor 0.13.2): a producer's **adapter** (:class:`HarborTaskAdapter` for
scored rollouts, :class:`PromptAdapter` for generation) writes each task dir
(`instruction.md` + `task.toml` \[+ `evsys_verifier.json` spec + a dummy
`tests/test.sh` when scored]) and returns harbor-native `TaskConfig`\s →
:func:`run_harbor_rollouts` builds a `JobConfig` over those `TaskConfig`\s ×
one `agent`, `n_attempts = num_samples` → `Job.run()` → harvest each
trial's `agent_result` (`rollout_details` + completion + token/cost usage)
and `verifier_result` (reward) into a :class:`Trajectory`.

The reward is produced by harbor running our
:class:`~evsys_sdk.training.harbor_agents.EvsysVerifier` (the job-level verifier)
**host-side, no container**: it wraps the task's registered verifier fn over the
completion the agent wrote. SHARED verifier mode (the default) keeps it in the
agent's no-op environment; the dummy `tests/test.sh` only satisfies harbor's
task-load check and is never executed. (Generation-only rollouts disable the
verifier and use `environment_mode="separate"` so no `test.sh` is needed.)

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['HarborTaskAdapter', 'PromptAdapter', 'run_harbor_rollouts']&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;HarborTaskAdapter&#x22;" href="&#x22;/docs/evsys_sdk/training/harbor_engine/HarborTaskAdapter&#x22;" />

      <Card title="&#x22;PromptAdapter&#x22;" href="&#x22;/docs/evsys_sdk/training/harbor_engine/PromptAdapter&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;_agent_import_and_kwargs&#x22;" type="&#x22;(model_client, *, agent_import_path, model_name, model_path, renderer_name, max_tokens, temperature, max_turns, system_prompt) -> tuple[str, dict[str, Any]]&#x22;">
      Pick the harbor agent + its kwargs for a rollout. Pure + harbor-free so
      the agent-selection logic is unit-testable.

      An explicit `agent_import_path` wins (fully self-configured agent, no
      kwargs). Otherwise it's always :class:`BasicLoopAgent`, parameterized by
      `model_client`: `"tinker"` (on-policy `TinkerLLM`, needs `model_path`)
      or `"litellm"` (closed/API model; `model_name` is a litellm string, the
      tinker-only `model_path`/`renderer_name` are ignored).

      <PySourceCode>
        ```python
        def _agent_import_and_kwargs(
            model_client: str,
            *,
            agent_import_path: str | None,
            model_name: str,
            model_path: str | None,
            renderer_name: str | None,
            max_tokens: int,
            temperature: float,
            max_turns: int,
            system_prompt: str | None,
        ) -> tuple[str, dict[str, Any]]:
            """Pick the harbor agent + its kwargs for a rollout. Pure + harbor-free so
            the agent-selection logic is unit-testable.

            An explicit ``agent_import_path`` wins (fully self-configured agent, no
            kwargs). Otherwise it's always :class:`BasicLoopAgent`, parameterized by
            ``model_client``: ``"tinker"`` (on-policy ``TinkerLLM``, needs ``model_path``)
            or ``"litellm"`` (closed/API model; ``model_name`` is a litellm string, the
            tinker-only ``model_path``/``renderer_name`` are ignored).
            """
            if agent_import_path:
                return agent_import_path, {}
            return f"{_AGENTS_PATH}:BasicLoopAgent", {
                "model_name": model_name,
                "model_path": model_path,
                "renderer_name": renderer_name,
                "max_tokens": max_tokens,
                "temperature": temperature,
                "max_turns": max_turns,
                "system_prompt": system_prompt,
                "model_client": model_client,
            }
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;model_client&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;agent_import_path&#x22;" type="&#x22;str | None&#x22;" value="null" />

        <PyParameter name="&#x22;model_name&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;model_path&#x22;" type="&#x22;str | None&#x22;" value="null" />

        <PyParameter name="&#x22;renderer_name&#x22;" type="&#x22;str | None&#x22;" value="null" />

        <PyParameter name="&#x22;max_tokens&#x22;" type="&#x22;int&#x22;" value="null" />

        <PyParameter name="&#x22;temperature&#x22;" type="&#x22;float&#x22;" value="null" />

        <PyParameter name="&#x22;max_turns&#x22;" type="&#x22;int&#x22;" value="null" />

        <PyParameter name="&#x22;system_prompt&#x22;" type="&#x22;str | None&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;tuple[str, dict[str, typing.Any]]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_to_agent_config&#x22;" type="&#x22;(AgentConfig, import_path, kwargs) -> Any&#x22;">
      Map `(import_path, agent kwargs)` → a harbor `AgentConfig`.

      harbor 0.13.2 passes `model_name` to the agent constructor from the
      top-level `AgentConfig.model_name` field, so it must NOT also live in
      `kwargs` (else the agent gets `model_name` twice). Lift it out here so
      the agent-selection logic above can stay a flat kwargs dict.

      <PySourceCode>
        ```python
        def _to_agent_config(AgentConfig: Any, import_path: str, kwargs: dict[str, Any]) -> Any:
            """Map ``(import_path, agent kwargs)`` → a harbor ``AgentConfig``.

            harbor 0.13.2 passes ``model_name`` to the agent constructor from the
            top-level ``AgentConfig.model_name`` field, so it must NOT also live in
            ``kwargs`` (else the agent gets ``model_name`` twice). Lift it out here so
            the agent-selection logic above can stay a flat kwargs dict."""
            kwargs = dict(kwargs)
            return AgentConfig(
                import_path=import_path,
                model_name=kwargs.pop("model_name", None),
                kwargs=kwargs,
            )
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;AgentConfig&#x22;" type="&#x22;Any&#x22;" value="null" />

        <PyParameter name="&#x22;import_path&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;kwargs&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;run_harbor_rollouts&#x22;" type="&#x22;(items, *, outcome_reward=True, model_name, model_path, workspace_dir, model_client='tinker', renderer_name=None, num_samples=1, max_turns=1, max_tokens=512, temperature=1.0, system_prompt=None, agent_import_path=None, n_concurrent=4, max_retries=2, _job_factory=None) -> list[TrajectoryGroup]&#x22;">
      Roll out `items` (× `num_samples`) through harbor's `Job` engine —
      one :class:`TrajectoryGroup` per item, in order.

      `outcome_reward` is the agent-meaningful knob — does the rollout get scored
      by an outcome verifier? The runner is adapter-aware (it runs the matching
      adapter to write the task dirs, where `materialize_task` used to be), so no
      caller ever touches an adapter:

      * `outcome_reward=True` (default) — `items` are :class:`HarborTask`\s;
        :class:`HarborTaskAdapter` writes scored task dirs and the host-side
        :class:`EvsysVerifier` produces each outcome reward. (RL + benchmark eval.)
      * `outcome_reward=False` — `items` are prompt strings; :class:`PromptAdapter`
        writes generation-only dirs (no verifier, `reward=0`). (SDFT students.)

      `model_client` — `"tinker"` (on-policy `TinkerLLM`, needs `model_path`)
      or `"litellm"` (closed/API model; `model_name` a litellm string, e.g.
      `"anthropic/claude-opus-4-1"`).

      `_job_factory` is the test seam: `async (job_config) -> job_result`.
      When `None`, harbor is imported and `Job.create(...).run()` is used.

      <PySourceCode>
        ```python
        async def run_harbor_rollouts(
            items: Sequence[Any],
            *,
            outcome_reward: bool = True,
            model_name: str,
            model_path: str | None,
            workspace_dir: Path,
            model_client: str = "tinker",
            renderer_name: str | None = None,
            num_samples: int = 1,
            max_turns: int = 1,
            max_tokens: int = 512,
            temperature: float = 1.0,
            system_prompt: str | None = None,
            agent_import_path: str | None = None,
            n_concurrent: int = 4,
            max_retries: int = 2,
            _job_factory: Any | None = None,
        ) -> list[TrajectoryGroup]:
            """Roll out ``items`` (× ``num_samples``) through harbor's ``Job`` engine —
            one :class:`TrajectoryGroup` per item, in order.

            ``outcome_reward`` is the agent-meaningful knob — does the rollout get scored
            by an outcome verifier? The runner is adapter-aware (it runs the matching
            adapter to write the task dirs, where ``materialize_task`` used to be), so no
            caller ever touches an adapter:

            * ``outcome_reward=True`` (default) — ``items`` are :class:`HarborTask`\\s;
              :class:`HarborTaskAdapter` writes scored task dirs and the host-side
              :class:`EvsysVerifier` produces each outcome reward. (RL + benchmark eval.)
            * ``outcome_reward=False`` — ``items`` are prompt strings; :class:`PromptAdapter`
              writes generation-only dirs (no verifier, ``reward=0``). (SDFT students.)

            ``model_client`` — ``"tinker"`` (on-policy ``TinkerLLM``, needs ``model_path``)
            or ``"litellm"`` (closed/API model; ``model_name`` a litellm string, e.g.
            ``"anthropic/claude-opus-4-1"``).

            ``_job_factory`` is the test seam: ``async (job_config) -> job_result``.
            When ``None``, harbor is imported and ``Job.create(...).run()`` is used.
            """
            workspace_dir.mkdir(parents=True, exist_ok=True)

            # Adapter-aware: our data → harbor task dirs + native TaskConfigs. The
            # outcome-reward mode picks the adapter (scored task vs generation prompt).
            adapter = (HarborTaskAdapter if outcome_reward else PromptAdapter)(items)
            task_configs = adapter.to_harbor(workspace_dir / "tasks")

            # Lazy harbor imports — keep this module importable without the extra.
            from harbor import Job
            from harbor.models.job.config import JobConfig, RetryConfig
            from harbor.models.trial.config import (
                AgentConfig,
                EnvironmentConfig,
                VerifierConfig,
            )

            import_path, agent_kwargs = _agent_import_and_kwargs(
                model_client,
                agent_import_path=agent_import_path,
                model_name=model_name,
                model_path=model_path,
                renderer_name=renderer_name,
                max_tokens=max_tokens,
                temperature=temperature,
                max_turns=max_turns,
                system_prompt=system_prompt,
            )
            agent = _to_agent_config(AgentConfig, import_path, agent_kwargs)
            config = JobConfig(
                tasks=task_configs,
                agents=[agent],
                environment=EnvironmentConfig(import_path=f"{_AGENTS_PATH}:NoOpEnvironment"),
                # outcome_reward: host-side EvsysVerifier wraps the registered fn (SHARED
                # mode, no container) → reward per trajectory. Else: no verifier, reward 0.
                verifier=(VerifierConfig(import_path=f"{_AGENTS_PATH}:EvsysVerifier")
                          if outcome_reward else VerifierConfig(disable=True)),
                jobs_dir=workspace_dir / "jobs",
                n_concurrent_trials=n_concurrent,
                n_attempts=num_samples,                    # repeats per task = samples
                retry=RetryConfig(max_retries=max_retries),
            )

            result = await (_job_factory(config) if _job_factory is not None
                            else _run_job(Job, config))
            return _harvest(result, task_configs)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;items&#x22;" type="&#x22;Sequence[Any]&#x22;" value="null" />

        <PyParameter name="&#x22;outcome_reward&#x22;" type="&#x22;bool&#x22;" value="&#x22;True&#x22;" />

        <PyParameter name="&#x22;model_name&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;model_path&#x22;" type="&#x22;str | None&#x22;" value="null" />

        <PyParameter name="&#x22;workspace_dir&#x22;" type="&#x22;Path&#x22;" value="null" />

        <PyParameter name="&#x22;model_client&#x22;" type="&#x22;str&#x22;" value="&#x22;'tinker'&#x22;" />

        <PyParameter name="&#x22;renderer_name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;num_samples&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />

        <PyParameter name="&#x22;max_turns&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />

        <PyParameter name="&#x22;max_tokens&#x22;" type="&#x22;int&#x22;" value="&#x22;512&#x22;" />

        <PyParameter name="&#x22;temperature&#x22;" type="&#x22;float&#x22;" value="&#x22;1.0&#x22;" />

        <PyParameter name="&#x22;system_prompt&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;agent_import_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;n_concurrent&#x22;" type="&#x22;int&#x22;" value="&#x22;4&#x22;" />

        <PyParameter name="&#x22;max_retries&#x22;" type="&#x22;int&#x22;" value="&#x22;2&#x22;" />

        <PyParameter name="&#x22;_job_factory&#x22;" type="&#x22;Any | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="&#x22;list[evsys_sdk.training.trajectory.TrajectoryGroup]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_run_job&#x22;" type="&#x22;(Job, config) -> Any&#x22;">
      <PySourceCode>
        ```python
        async def _run_job(Job: Any, config: Any) -> Any:
            job = await Job.create(config)
            return await job.run()
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;Job&#x22;" type="&#x22;Any&#x22;" value="null" />

        <PyParameter name="&#x22;config&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_trials_by_task&#x22;" type="&#x22;(job_result) -> dict[str, list[Any]]&#x22;">
      Group a job's trial results by `task_name` (the materialized dir's
      basename = `_safe(task_id)`). `n_attempts` trials share a task\_name.

      <PySourceCode>
        ```python
        def _trials_by_task(job_result: Any) -> dict[str, list[Any]]:
            """Group a job's trial results by ``task_name`` (the materialized dir's
            basename = ``_safe(task_id)``). ``n_attempts`` trials share a task_name."""
            out: dict[str, list[Any]] = {}
            for tr in (getattr(job_result, "trial_results", None) or []):
                out.setdefault(getattr(tr, "task_name", None), []).append(tr)
            return out
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;job_result&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;dict[str, list[typing.Any]]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_harvest&#x22;" type="&#x22;(job_result, task_configs) -> list[TrajectoryGroup]&#x22;">
      <PySourceCode>
        ```python
        def _harvest(job_result: Any, task_configs: Sequence[Any]) -> list[TrajectoryGroup]:
            by_task = _trials_by_task(job_result)
            groups: list[TrajectoryGroup] = []
            for tc in task_configs:
                # harbor derives a trial's ``task_name`` from the task dir basename.
                task_name = Path(tc.path).name
                trajs = [
                    traj for tr in by_task.get(task_name, [])
                    if (traj := _trial_to_trajectory(tr)) is not None
                ]
                groups.append(TrajectoryGroup(trajectories=trajs))
            return groups
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;job_result&#x22;" type="&#x22;Any&#x22;" value="null" />

        <PyParameter name="&#x22;task_configs&#x22;" type="&#x22;Sequence[Any]&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;list[evsys_sdk.training.trajectory.TrajectoryGroup]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_trial_to_trajectory&#x22;" type="&#x22;(tr) -> Trajectory | None&#x22;">
      Convert a harbor `TrialResult` → our multi-turn :class:`Trajectory`,
      reading the rollout off `agent_result` (`AgentContext`).

      Token-level turns come from `rollout_details` (tinker on-policy rollouts).
      Closed/API models (litellm) return no token ids, so for an eval trial — one
      that produced a verifier reward — we still build a *token-less* Trajectory
      carrying the reward + usage so it isn't dropped from scoring. Errored trials,
      and generation-only trials with neither tokens nor a reward, return `None`.

      <PySourceCode>
        ```python
        def _trial_to_trajectory(tr: Any) -> Trajectory | None:
            """Convert a harbor ``TrialResult`` → our multi-turn :class:`Trajectory`,
            reading the rollout off ``agent_result`` (``AgentContext``).

            Token-level turns come from ``rollout_details`` (tinker on-policy rollouts).
            Closed/API models (litellm) return no token ids, so for an eval trial — one
            that produced a verifier reward — we still build a *token-less* Trajectory
            carrying the reward + usage so it isn't dropped from scoring. Errored trials,
            and generation-only trials with neither tokens nor a reward, return ``None``.
            """
            if tr is None or getattr(tr, "exception_info", None):
                return None
            agent_result = getattr(tr, "agent_result", None)
            details = getattr(agent_result, "rollout_details", None) if agent_result else None
            rd = details[0] if details else {}   # main chat history; empty for API models
            prompt_turns = rd.get("prompt_token_ids") or []
            completion_turns = rd.get("completion_token_ids") or []
            logprob_turns = rd.get("logprobs") or []

            rewards = getattr(getattr(tr, "verifier_result", None), "rewards", None)
            if not completion_turns and rewards is None:
                return None  # no tokens and no score → nothing to harvest (generation / failed)

            turns: list[Turn] = []
            for i, completion in enumerate(completion_turns):
                turns.append(Turn(
                    prompt_tokens=list(prompt_turns[i]) if i < len(prompt_turns) else [],
                    completion_tokens=list(completion),
                    logprobs=list(logprob_turns[i]) if i < len(logprob_turns) else [],
                ))

            usage = _trial_usage(tr)
            if usage["prompt_tokens"] is None:
                usage["prompt_tokens"] = sum(len(t.prompt_tokens) for t in turns)
            if usage["completion_tokens"] is None:
                usage["completion_tokens"] = sum(len(t.completion_tokens) for t in turns)

            # Reward from harbor's verifier (our host-side EvsysVerifier); 0.0 for
            # generation-only rollouts (verifier disabled) or when absent.
            reward = float((rewards or {}).get("reward", 0.0))
            return Trajectory(turns=turns, reward=reward, metadata={"usage": usage})
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;tr&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;evsys_sdk.training.trajectory.Trajectory | None&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_trial_usage&#x22;" type="&#x22;(tr) -> dict[str, Any]&#x22;">
      Pull harbor's native cost / token / timing info off a trial result.

      Harbor records `cost_usd` + token counts on `agent_result`
      (`AgentContext`) and per-phase wall-clock timing on the trial
      (`agent_execution`, whole-trial span as fallback). Any field harbor didn't
      populate stays `None` — on-policy tinker has no API `cost_usd`, and the
      caller backfills token counts from the turns. Pure + harbor-free.

      <PySourceCode>
        ```python
        def _trial_usage(tr: Any) -> dict[str, Any]:
            """Pull harbor's native cost / token / timing info off a trial result.

            Harbor records ``cost_usd`` + token counts on ``agent_result``
            (``AgentContext``) and per-phase wall-clock timing on the trial
            (``agent_execution``, whole-trial span as fallback). Any field harbor didn't
            populate stays ``None`` — on-policy tinker has no API ``cost_usd``, and the
            caller backfills token counts from the turns. Pure + harbor-free."""
            ar = getattr(tr, "agent_result", None)
            return {
                "cost_usd": getattr(ar, "cost_usd", None),
                "prompt_tokens": getattr(ar, "n_input_tokens", None),
                "completion_tokens": getattr(ar, "n_output_tokens", None),
                "cached_tokens": getattr(ar, "n_cache_tokens", None),
                "latency_s": _phase_seconds(getattr(tr, "agent_execution", None))
                or _phase_seconds(tr),
            }
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;tr&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;dict[str, typing.Any]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_phase_seconds&#x22;" type="&#x22;(phase) -> float | None&#x22;">
      Wall-clock seconds for a harbor timing phase — anything carrying
      `started_at` / `finished_at` datetimes. `None` when either is missing.

      <PySourceCode>
        ```python
        def _phase_seconds(phase: Any) -> float | None:
            """Wall-clock seconds for a harbor timing phase — anything carrying
            ``started_at`` / ``finished_at`` datetimes. ``None`` when either is missing."""
            started = getattr(phase, "started_at", None)
            finished = getattr(phase, "finished_at", None)
            if started is None or finished is None:
                return None
            try:
                return (finished - started).total_seconds()
            except (TypeError, AttributeError):  # pragma: no cover - defensive
                return None
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;phase&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;float | None&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_safe&#x22;" type="&#x22;(name) -> str&#x22;">
      <PySourceCode>
        ```python
        def _safe(name: str) -> str:
            return "".join(c if (c.isalnum() or c in "-_") else "_" for c in str(name))
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;str&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
