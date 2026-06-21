# DashboardClient (/docs/evsys_sdk/dashboard_client/DashboardClient)



HTTP client for the EvolvingSystems backend's SDK write routes, with a local
mirror and graceful degradation when the backend is unreachable.

## Attributes [#attributes]

<PyAttribute name="&#x22;base_url&#x22;" type="null" value="&#x22;(base_url or os.environ.get(EVSYS_API_URL_ENV) or DEFAULT_API_URL).rstrip('/')&#x22;" />

<PyAttribute name="&#x22;api_key&#x22;" type="null" value="&#x22;api_key or os.environ.get(EVSYS_API_KEY_ENV)&#x22;" />

<PyAttribute name="&#x22;project_id&#x22;" type="null" value="&#x22;project_id or os.environ.get(EVSYS_PROJECT_ID_ENV)&#x22;" />

<PyAttribute name="&#x22;timeout_s&#x22;" type="null" value="&#x22;timeout_s&#x22;" />

<PyAttribute name="&#x22;offline&#x22;" type="null" value="&#x22;offline if offline is not None else truthy_env(os.environ.get(EVSYS_OFFLINE_ENV))&#x22;" />

<PyAttribute name="&#x22;local&#x22;" type="null" value="&#x22;LocalExperimentStore(log_dir=log_dir)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, base_url=None, api_key=None, project_id=None, timeout_s=DEFAULT_TIMEOUT_S, offline=None, log_dir=None) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(
        self,
        *,
        base_url: str | None = None,
        api_key: str | None = None,
        project_id: str | None = None,
        timeout_s: float = DEFAULT_TIMEOUT_S,
        offline: bool | None = None,
        log_dir: str | None = None,
    ) -> None:
        self.base_url = (base_url or os.environ.get(EVSYS_API_URL_ENV) or DEFAULT_API_URL).rstrip("/")
        self.api_key = api_key or os.environ.get(EVSYS_API_KEY_ENV)
        self.project_id = project_id or os.environ.get(EVSYS_PROJECT_ID_ENV)
        self.timeout_s = timeout_s
        self.offline = offline if offline is not None else truthy_env(os.environ.get(EVSYS_OFFLINE_ENV))

        # Always-on local mirror.
        self.local = LocalExperimentStore(log_dir=log_dir)

        if self.offline:
            log.info(
                "evsys_sdk running in OFFLINE mode — writing only to %s",
                self.local.root,
            )
            self._session = None
            return

        # Online mode requires credentials.
        missing = []
        if not self.api_key:
            missing.append(EVSYS_API_KEY_ENV)
        if not self.project_id:
            missing.append(EVSYS_PROJECT_ID_ENV)
        if missing:
            raise EvsysAuthError(
                "DashboardClient is not authenticated: missing "
                + " and ".join(missing)
                + ". Set them (the API key from the dashboard at Settings → API keys, "
                "and the project id shared by your project), or run offline with "
                f"{EVSYS_OFFLINE_ENV}=true to log locally without auth."
            )

        self._session = requests.Session()
        self._session.headers.update({
            HEADER_AUTHORIZATION: bearer(self.api_key),
            HEADER_CONTENT_TYPE: CONTENT_TYPE_JSON,
        })
        # Flips to True after the first connection failure so we stop hammering
        # an unreachable backend within a single run.
        self._remote_down = False
        log.debug("DashboardClient online | base_url=%s project_id=%s", self.base_url, self.project_id)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;base_url&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;api_key&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;project_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;timeout_s&#x22;" type="&#x22;float&#x22;" value="&#x22;DEFAULT_TIMEOUT_S&#x22;" />

    <PyParameter name="&#x22;offline&#x22;" type="&#x22;bool | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;log_dir&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_post&#x22;" type="&#x22;(self, path, body=None) -> dict | None&#x22;">
  POST to the backend. Returns the JSON dict, or None when the write
  could not be sent remotely (offline, or backend unreachable). Raises
  DashboardClientError on a 4xx response.

  <PySourceCode>
    ```python
    def _post(self, path: str, body: dict | None = None) -> dict | None:
        """POST to the backend. Returns the JSON dict, or None when the write
        could not be sent remotely (offline, or backend unreachable). Raises
        DashboardClientError on a 4xx response.
        """
        if self.offline or self._session is None or self._remote_down:
            return None
        url = f"{self.base_url}{API_PREFIX}{path}"
        try:
            r = self._session.post(url, json=body or {}, timeout=self.timeout_s)
        except requests.exceptions.RequestException as e:
            self._remote_down = True
            log.warning(
                "backend unreachable (%s) — falling back to local mirror at %s for the rest of this run",
                e.__class__.__name__,
                self.local.root,
            )
            return None
        if 200 <= r.status_code < 300:
            try:
                return r.json()
            except Exception:
                return {KEY_RAW: r.text}
        if 400 <= r.status_code < 500:
            # Real client error (auth, not-a-member, bad payload): surface it.
            raise DashboardClientError(r.status_code, r.text, path)
        # 5xx — treat like an outage and degrade to local.
        log.warning("backend error HTTP %s on %s — falling back to local mirror", r.status_code, path)
        self._remote_down = True
        return None
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;path&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;body&#x22;" type="&#x22;dict | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict | None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;create_experiment&#x22;" type="&#x22;(self, *, experiment_name, hypothesis=None, hypothesis_reasoning=None, plan=None, tags=None, project_goal_id=None, config=None, declaration_path=None, is_valid=None) -> dict&#x22;">
  <PySourceCode>
    ```python
    def create_experiment(
        self,
        *,
        experiment_name: str,
        hypothesis: str | None = None,
        hypothesis_reasoning: str | None = None,
        plan: str | None = None,
        tags: list[str] | None = None,
        project_goal_id: str | None = None,
        config: dict | None = None,
        declaration_path: str | None = None,
        is_valid: bool | None = None,
    ) -> dict:
        # NB: base_model is a per-run hyperparameter (runs.run_config), not an
        # experiment field. client / problem_statement_id / parent_experiment_id /
        # seed_config were dropped from the schema (D1/D3/D18).
        body: dict = {"experiment_name": experiment_name}
        if self.project_id:
            body[FIELD_PROJECT_ID] = self.project_id
        for k, v in (
            ("hypothesis", hypothesis),
            ("hypothesis_reasoning", hypothesis_reasoning), ("plan", plan),
            ("tags", tags), ("project_goal_id", project_goal_id),
            ("config", config), ("declaration_path", declaration_path),
            ("is_valid", is_valid),
        ):
            if v is not None:
                body[k] = v

        resp = self._post(EP_CREATE_EXPERIMENT, body)
        exp = (resp or {}).get(KEY_EXPERIMENT) or {}
        exp_id = exp.get("id") or _new_id()
        exp = {**body, **exp, "id": exp_id}
        self.local.create_experiment(exp_id, body)
        return exp
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;experiment_name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;hypothesis&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;hypothesis_reasoning&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;plan&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;tags&#x22;" type="&#x22;list[str] | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;project_goal_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;config&#x22;" type="&#x22;dict | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;declaration_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;is_valid&#x22;" type="&#x22;bool | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;update_experiment&#x22;" type="&#x22;(self, experiment_id, **patch) -> dict&#x22;">
  PATCH experiment metadata. Whitelisted backend fields: status,
  best\_score, best\_generation\_id, current\_iteration, error\_message,
  hypothesis, hypothesis\_reasoning, plan, conclusion, tags,
  problem\_statement\_id, is\_valid.

  e.g. `update_experiment(exp_id, is_valid=False)` to invalidate an
  experiment after a bug is found in its runs (D16).

  <PySourceCode>
    ```python
    def update_experiment(self, experiment_id: str, **patch: Any) -> dict:
        """PATCH experiment metadata. Whitelisted backend fields: status,
        best_score, best_generation_id, current_iteration, error_message,
        hypothesis, hypothesis_reasoning, plan, conclusion, tags,
        problem_statement_id, is_valid.

        e.g. ``update_experiment(exp_id, is_valid=False)`` to invalidate an
        experiment after a bug is found in its runs (D16).
        """
        resp = self._post(EP_UPDATE_EXPERIMENT.format(experiment_id=experiment_id), patch)
        self.local.update_experiment(experiment_id, patch)
        return resp or {"id": experiment_id, **patch}
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;experiment_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;patch&#x22;" type="&#x22;Any&#x22;" value="&#x22;{}&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;create_run&#x22;" type="&#x22;(self, *, experiment_id, group_id=None, dataset_id=None, seed=None, recipe_kind=None, run_config=None, status=STATUS_PENDING, wandb_run_url=None, tensorboard_path=None, tensorboard_archive_url=None) -> dict&#x22;">
  <PySourceCode>
    ```python
    def create_run(
        self,
        *,
        experiment_id: str,
        group_id: str | None = None,
        dataset_id: str | None = None,
        seed: int | None = None,
        recipe_kind: str | None = None,
        run_config: dict | None = None,
        status: str = STATUS_PENDING,
        wandb_run_url: str | None = None,
        tensorboard_path: str | None = None,
        tensorboard_archive_url: str | None = None,
    ) -> dict:
        body: dict = {"experiment_id": experiment_id, "status": status}
        for k, v in (
            ("group_id", group_id), ("dataset_id", dataset_id), ("seed", seed),
            ("recipe_kind", recipe_kind), ("run_config", run_config),
            ("wandb_run_url", wandb_run_url), ("tensorboard_path", tensorboard_path),
            ("tensorboard_archive_url", tensorboard_archive_url),
        ):
            if v is not None:
                body[k] = v

        resp = self._post(EP_CREATE_RUN, body)
        run = (resp or {}).get(KEY_RUN) or {}
        run_id = run.get("id") or _new_id()
        run = {**body, **run, "id": run_id}
        self.local.create_run(run_id, body)
        return run
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;experiment_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;group_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;dataset_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;seed&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;recipe_kind&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;run_config&#x22;" type="&#x22;dict | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;status&#x22;" type="&#x22;str&#x22;" value="&#x22;STATUS_PENDING&#x22;" />

    <PyParameter name="&#x22;wandb_run_url&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;tensorboard_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;tensorboard_archive_url&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;update_run&#x22;" type="&#x22;(self, run_id, **patch) -> dict&#x22;">
  <PySourceCode>
    ```python
    def update_run(self, run_id: str, **patch: Any) -> dict:
        resp = self._post(EP_UPDATE_RUN.format(run_id=run_id), patch)
        self.local.update_run(run_id, patch)
        return resp or {"id": run_id, **patch}
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;patch&#x22;" type="&#x22;Any&#x22;" value="&#x22;{}&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;log_step_metric&#x22;" type="&#x22;(self, run_id, *, step, split='train', loss=None, accuracy=None, learning_rate=None, grad_norm=None, tokens_per_sec=None, **metrics) -> dict&#x22;">
  Log per-step training metrics as long-format run\_metrics (D15).

  Accepts arbitrary named series via `**metrics` (e.g. `val_loss=…`,
  `kl_loss=…`) plus a `split` (`train`/`val`/`test`). The named
  kwargs (loss, accuracy, …) are folded into the same `metrics` map.
  None values are dropped.

  <PySourceCode>
    ```python
    def log_step_metric(
        self,
        run_id: str,
        *,
        step: int,
        split: str = "train",
        loss: float | None = None,
        accuracy: float | None = None,
        learning_rate: float | None = None,
        grad_norm: float | None = None,
        tokens_per_sec: float | None = None,
        **metrics: float,
    ) -> dict:
        """Log per-step training metrics as long-format run_metrics (D15).

        Accepts arbitrary named series via ``**metrics`` (e.g. ``val_loss=…``,
        ``kl_loss=…``) plus a ``split`` (``train``/``val``/``test``). The named
        kwargs (loss, accuracy, …) are folded into the same ``metrics`` map.
        None values are dropped.
        """
        named = {
            "loss": loss, "accuracy": accuracy, "learning_rate": learning_rate,
            "grad_norm": grad_norm, "tokens_per_sec": tokens_per_sec,
        }
        merged: dict[str, float] = {
            k: v for k, v in {**named, **metrics}.items() if v is not None
        }
        body: dict = {"step": int(step), "split": split, "metrics": merged}
        resp = self._post(EP_LOG_METRICS.format(run_id=run_id), body)
        self.local.log_step(run_id, body)
        return resp or {"ok": True}
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int&#x22;" value="null" />

    <PyParameter name="&#x22;split&#x22;" type="&#x22;str&#x22;" value="&#x22;'train'&#x22;" />

    <PyParameter name="&#x22;loss&#x22;" type="&#x22;float | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;accuracy&#x22;" type="&#x22;float | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;learning_rate&#x22;" type="&#x22;float | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;grad_norm&#x22;" type="&#x22;float | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;tokens_per_sec&#x22;" type="&#x22;float | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;float&#x22;" value="&#x22;{}&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;create_eval&#x22;" type="&#x22;(self, run_id, *, metrics, benchmark_id=None, checkpoint_id=None, model_ref=None, step=None, breakdowns=None, sdk_version=None) -> dict&#x22;">
  Record an eval (D13): a run scored on a benchmark → metrics\{name:value}.

  <PySourceCode>
    ```python
    def create_eval(
        self,
        run_id: str,
        *,
        metrics: dict[str, float],
        benchmark_id: str | None = None,
        checkpoint_id: str | None = None,
        model_ref: str | None = None,
        step: int | None = None,
        breakdowns: dict | None = None,
        sdk_version: str | None = None,
    ) -> dict:
        """Record an eval (D13): a run scored on a benchmark → metrics{name:value}."""
        body: dict = {"metrics": dict(metrics)}
        for k, v in (
            ("benchmark_id", benchmark_id), ("checkpoint_id", checkpoint_id),
            ("model_ref", model_ref), ("step", step), ("breakdowns", breakdowns),
            ("sdk_version", sdk_version),
        ):
            if v is not None:
                body[k] = v
        resp = self._post(EP_LOG_EVAL.format(run_id=run_id), body)
        self.local.log_eval(run_id, body)
        return resp or {"ok": True}
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="null" />

    <PyParameter name="&#x22;benchmark_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;checkpoint_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;model_ref&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;breakdowns&#x22;" type="&#x22;dict | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;sdk_version&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;add_checkpoint&#x22;" type="&#x22;(self, run_id, *, uri, label=None, step=None, base_model=None, is_final=False) -> dict&#x22;">
  Record a named checkpoint for a run (D7).

  <PySourceCode>
    ```python
    def add_checkpoint(
        self,
        run_id: str,
        *,
        uri: str,
        label: str | None = None,
        step: int | None = None,
        base_model: str | None = None,
        is_final: bool = False,
    ) -> dict:
        """Record a named checkpoint for a run (D7)."""
        body: dict = {"uri": uri, "is_final": is_final}
        for k, v in (("label", label), ("step", step), ("base_model", base_model)):
            if v is not None:
                body[k] = v
        resp = self._post(EP_ADD_CHECKPOINT.format(run_id=run_id), body)
        return resp or {"ok": True}
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;uri&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;label&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;base_model&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;is_final&#x22;" type="&#x22;bool&#x22;" value="&#x22;False&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;log_predictions&#x22;" type="&#x22;(self, run_id, predictions) -> dict&#x22;">
  Bulk-insert per-task predictions. Each is:
  \{ kind: 'eval' | 'rollout', eval\_id?, task\_id?, sample\_idx?, step?,
  instruction, model\_output, expected?, reward?, advantage?, metadata? }

  <PySourceCode>
    ```python
    def log_predictions(
        self,
        run_id: str,
        predictions: list[dict],
    ) -> dict:
        """Bulk-insert per-task predictions. Each is:
            { kind: 'eval' | 'rollout', eval_id?, task_id?, sample_idx?, step?,
              instruction, model_output, expected?, reward?, advantage?, metadata? }
        """
        if not predictions:
            return {"ok": True, "inserted": 0}
        resp = self._post(
            EP_LOG_PREDICTIONS.format(run_id=run_id),
            {"predictions": predictions},
        )
        self.local.log_predictions(run_id, predictions)
        return resp or {"ok": True, "inserted": len(predictions)}
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;predictions&#x22;" type="&#x22;list[dict]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>
