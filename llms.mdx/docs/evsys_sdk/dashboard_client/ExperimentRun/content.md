# ExperimentRun (/docs/evsys_sdk/dashboard_client/ExperimentRun)



One generation in one experiment, lifecycle-managed.

Use as a context manager. On clean exit, the generation + experiment are
marked completed (with the best score / generation\_id). On raised
exception, both are marked failed with the exception message. All writes
are mirrored locally and degrade gracefully if the backend is down.

## Attributes [#attributes]

<PyAttribute name="&#x22;client&#x22;" type="null" value="&#x22;client&#x22;" />

<PyAttribute name="&#x22;experiment_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;run_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, client, *, experiment_name, hypothesis=None, hypothesis_reasoning=None, plan=None, tags=None, project_goal_id=None, group_id=None, dataset_id=None, seed=None, recipe_kind=None, run_config=None, wandb_run_url=None, tensorboard_path=None, experiment_id=None) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(
        self,
        client: DashboardClient,
        *,
        experiment_name: str,
        hypothesis: str | None = None,
        hypothesis_reasoning: str | None = None,
        plan: str | None = None,
        tags: list[str] | None = None,
        project_goal_id: str | None = None,
        # run-level
        group_id: str | None = None,
        dataset_id: str | None = None,
        seed: int | None = None,
        recipe_kind: str | None = None,
        run_config: dict | None = None,
        wandb_run_url: str | None = None,
        tensorboard_path: str | None = None,
        # threading an existing experiment for multi-run campaigns
        experiment_id: str | None = None,
    ) -> None:
        self.client = client
        self._exp_kwargs = {
            "experiment_name": experiment_name,
            "hypothesis": hypothesis,
            "hypothesis_reasoning": hypothesis_reasoning,
            "plan": plan,
            "tags": tags,
            "project_goal_id": project_goal_id,
        }
        self._run_kwargs = {
            "group_id": group_id,
            "dataset_id": dataset_id,
            "seed": seed,
            "recipe_kind": recipe_kind,
            "run_config": run_config,
            "wandb_run_url": wandb_run_url,
            "tensorboard_path": tensorboard_path,
        }
        self._given_experiment_id = experiment_id
        self.experiment_id: str | None = None
        self.run_id: str | None = None
        self._best_score: float | None = None
        self._conclusion: str | None = None
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;client&#x22;" type="&#x22;DashboardClient&#x22;" value="null" />

    <PyParameter name="&#x22;experiment_name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;hypothesis&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;hypothesis_reasoning&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;plan&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;tags&#x22;" type="&#x22;list[str] | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;project_goal_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;group_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;dataset_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;seed&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;recipe_kind&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;run_config&#x22;" type="&#x22;dict | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;wandb_run_url&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;tensorboard_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;experiment_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;__enter__&#x22;" type="&#x22;(self) -> 'ExperimentRun'&#x22;">
  <PySourceCode>
    ```python
    def __enter__(self) -> "ExperimentRun":
        if self._given_experiment_id:
            self.experiment_id = self._given_experiment_id
        else:
            exp = self.client.create_experiment(**{k: v for k, v in self._exp_kwargs.items() if v is not None})  # type: ignore[arg-type]
            self.experiment_id = exp["id"]
        assert self.experiment_id is not None
        run = self.client.create_run(
            experiment_id=self.experiment_id,
            status=STATUS_RUNNING,
            **{k: v for k, v in self._run_kwargs.items() if v is not None},  # type: ignore[arg-type]
        )
        self.run_id = run["id"]
        return self
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;'ExperimentRun'&#x22;" />
</PyFunction>

<PyFunction name="&#x22;__exit__&#x22;" type="&#x22;(self, exc_type, exc_val, exc_tb) -> None&#x22;">
  <PySourceCode>
    ```python
    def __exit__(self, exc_type, exc_val, exc_tb) -> None:
        if exc_type is not None:
            msg = f"{exc_type.__name__}: {exc_val}" if exc_val else exc_type.__name__
            if self.run_id:
                try:
                    self.client.update_run(self.run_id, status=STATUS_FAILED, **{FIELD_ERROR_MESSAGE: msg})
                except Exception:
                    pass
            if self.experiment_id:
                try:
                    self.client.update_experiment(self.experiment_id, status=STATUS_FAILED, **{FIELD_ERROR_MESSAGE: msg})
                except Exception:
                    pass
            return None
        # Clean exit. best_score lives on the experiment; the run's headline
        # number (if any) goes in runs.summary.
        if self.run_id:
            run_patch: dict = {"status": STATUS_COMPLETED}
            if self._best_score is not None:
                run_patch["summary"] = {"best_score": self._best_score}
            self.client.update_run(self.run_id, **run_patch)
        if self.experiment_id:
            exp_patch: dict = {"status": STATUS_COMPLETED}
            if self._best_score is not None:
                exp_patch[FIELD_BEST_SCORE] = self._best_score
            if self._conclusion is not None:
                exp_patch[FIELD_CONCLUSION] = self._conclusion
            self.client.update_experiment(self.experiment_id, **exp_patch)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;exc_type&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;exc_val&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;exc_tb&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;log_step&#x22;" type="&#x22;(self, step, **metrics) -> None&#x22;">
  <PySourceCode>
    ```python
    def log_step(self, step: int, **metrics: Any) -> None:
        assert self.run_id
        self.client.log_step_metric(self.run_id, step=step, **metrics)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int&#x22;" value="null" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;Any&#x22;" value="&#x22;{}&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;log_eval&#x22;" type="&#x22;(self, *, metrics, benchmark_id=None, checkpoint_id=None, model_ref=None, step=None) -> None&#x22;">
  <PySourceCode>
    ```python
    def log_eval(
        self,
        *,
        metrics: dict[str, float],
        benchmark_id: str | None = None,
        checkpoint_id: str | None = None,
        model_ref: str | None = None,
        step: int | None = None,
    ) -> None:
        assert self.run_id
        self.client.create_eval(self.run_id, metrics=metrics, benchmark_id=benchmark_id,
                                checkpoint_id=checkpoint_id, model_ref=model_ref, step=step)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="null" />

    <PyParameter name="&#x22;benchmark_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;checkpoint_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;model_ref&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;add_checkpoint&#x22;" type="&#x22;(self, *, uri, label=None, step=None, base_model=None, is_final=False) -> None&#x22;">
  <PySourceCode>
    ```python
    def add_checkpoint(self, *, uri: str, label: str | None = None, step: int | None = None,
                       base_model: str | None = None, is_final: bool = False) -> None:
        assert self.run_id
        self.client.add_checkpoint(self.run_id, uri=uri, label=label, step=step,
                                   base_model=base_model, is_final=is_final)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;uri&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;label&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;base_model&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;is_final&#x22;" type="&#x22;bool&#x22;" value="&#x22;False&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;log_predictions&#x22;" type="&#x22;(self, predictions) -> None&#x22;">
  <PySourceCode>
    ```python
    def log_predictions(self, predictions: list[dict]) -> None:
        assert self.run_id
        self.client.log_predictions(self.run_id, predictions)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;predictions&#x22;" type="&#x22;list[dict]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;set_best_score&#x22;" type="&#x22;(self, score) -> None&#x22;">
  <PySourceCode>
    ```python
    def set_best_score(self, score: float) -> None:
        self._best_score = float(score)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;score&#x22;" type="&#x22;float&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;set_conclusion&#x22;" type="&#x22;(self, text) -> None&#x22;">
  One- or two-line takeaway from the run. Flushed on clean **exit**.

  <PySourceCode>
    ```python
    def set_conclusion(self, text: str) -> None:
        """One- or two-line takeaway from the run. Flushed on clean __exit__."""
        self._conclusion = str(text)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;text&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;update_run&#x22;" type="&#x22;(self, **patch) -> None&#x22;">
  <PySourceCode>
    ```python
    def update_run(self, **patch: Any) -> None:
        assert self.run_id
        self.client.update_run(self.run_id, **patch)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;patch&#x22;" type="&#x22;Any&#x22;" value="&#x22;{}&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;update_experiment&#x22;" type="&#x22;(self, **patch) -> None&#x22;">
  <PySourceCode>
    ```python
    def update_experiment(self, **patch: Any) -> None:
        assert self.experiment_id
        self.client.update_experiment(self.experiment_id, **patch)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;patch&#x22;" type="&#x22;Any&#x22;" value="&#x22;{}&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
