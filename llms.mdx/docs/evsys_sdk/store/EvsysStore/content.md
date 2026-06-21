# EvsysStore (/docs/evsys_sdk/store/EvsysStore)



## Attributes [#attributes]

<PyAttribute name="&#x22;base_url&#x22;" type="null" value="&#x22;(base_url or os.environ.get(EVSYS_API_URL_ENV) or DEFAULT_API_URL).rstrip('/')&#x22;" />

<PyAttribute name="&#x22;api_key&#x22;" type="null" value="&#x22;api_key or os.environ.get(EVSYS_API_KEY_ENV)&#x22;" />

<PyAttribute name="&#x22;project_id&#x22;" type="null" value="&#x22;project_id or os.environ.get(EVSYS_PROJECT_ID_ENV)&#x22;" />

<PyAttribute name="&#x22;timeout_s&#x22;" type="null" value="&#x22;timeout_s&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, base_url=None, api_key=None, project_id=None, timeout_s=DEFAULT_TIMEOUT_S) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, *, base_url: str | None = None, api_key: str | None = None,
                 project_id: str | None = None, timeout_s: float = DEFAULT_TIMEOUT_S) -> None:
        self.base_url = (base_url or os.environ.get(EVSYS_API_URL_ENV)
                         or DEFAULT_API_URL).rstrip("/")
        self.api_key = api_key or os.environ.get(EVSYS_API_KEY_ENV)
        self.project_id = project_id or os.environ.get(EVSYS_PROJECT_ID_ENV)
        self.timeout_s = timeout_s
        if not self.api_key:
            raise EvsysStoreError(0, "missing EVSYS_API_KEY")
        self._endpoint = f"{self.base_url}{_GATEWAY}"
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;base_url&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;api_key&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;project_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;timeout_s&#x22;" type="&#x22;float&#x22;" value="&#x22;DEFAULT_TIMEOUT_S&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_call&#x22;" type="&#x22;(self, op, **args) -> Any&#x22;">
  <PySourceCode>
    ```python
    def _call(self, op: str, **args: Any) -> Any:
        clean = {k: v for k, v in args.items() if v is not None}
        r = requests.post(
            self._endpoint,
            headers={"Authorization": bearer(self.api_key), "Content-Type": "application/json"},
            json={"op": op, "args": clean},
            timeout=self.timeout_s,
        )
        if r.status_code >= 400:
            raise EvsysStoreError(r.status_code, r.text)
        return (r.json() or {}).get("result")
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;op&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;args&#x22;" type="&#x22;Any&#x22;" value="&#x22;{}&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_project&#x22;" type="&#x22;(self, project_id) -> str | None&#x22;">
  <PySourceCode>
    ```python
    def _project(self, project_id: str | None) -> str | None:
        return project_id or self.project_id
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;project_id&#x22;" type="&#x22;str | None&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str | None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;create_project&#x22;" type="&#x22;(self, name, *, description=None, organization_id=None) -> dict&#x22;">
  <PySourceCode>
    ```python
    def create_project(self, name: str, *, description: str | None = None,
                        organization_id: str | None = None) -> dict:
        return self._call("create_project", name=name, description=description,
                          organization_id=organization_id)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;description&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;organization_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;get_project&#x22;" type="&#x22;(self, project_id=None) -> dict | None&#x22;">
  <PySourceCode>
    ```python
    def get_project(self, project_id: str | None = None) -> dict | None:
        return self._call("get_project", project_id=self._project(project_id))
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;project_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict | None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;delete_project&#x22;" type="&#x22;(self, project_id=None) -> Any&#x22;">
  <PySourceCode>
    ```python
    def delete_project(self, project_id: str | None = None) -> Any:
        return self._call("delete_project", project_id=self._project(project_id))
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;project_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
</PyFunction>

<PyFunction name="&#x22;set_goal&#x22;" type="&#x22;(self, goal, *, project_id=None) -> dict&#x22;">
  <PySourceCode>
    ```python
    def set_goal(self, goal: str, *, project_id: str | None = None) -> dict:
        return self._call("set_goal", project_id=self._project(project_id), goal=goal)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;goal&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;project_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;list_goals&#x22;" type="&#x22;(self, project_id=None) -> list[dict]&#x22;">
  <PySourceCode>
    ```python
    def list_goals(self, project_id: str | None = None) -> list[dict]:
        return self._call("list_goals", project_id=self._project(project_id))
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;project_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;list[dict]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;current_goal&#x22;" type="&#x22;(self, project_id=None) -> dict | None&#x22;">
  <PySourceCode>
    ```python
    def current_goal(self, project_id: str | None = None) -> dict | None:
        return self._call("current_goal", project_id=self._project(project_id))
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;project_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict | None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;create_experiment&#x22;" type="&#x22;(self, *, experiment_name, project_id=None, hypothesis=None, project_goal_id=None, tags=None, **extra) -> dict&#x22;">
  <PySourceCode>
    ```python
    def create_experiment(self, *, experiment_name: str, project_id: str | None = None,
                          hypothesis: str | None = None, project_goal_id: str | None = None,
                          tags: list[str] | None = None, **extra: Any) -> dict:
        # user_id is set by the backend from the authenticated API key.
        return self._call("create_experiment", project_id=self._project(project_id),
                          experiment_name=experiment_name, hypothesis=hypothesis,
                          project_goal_id=project_goal_id, tags=tags, **extra)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;experiment_name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;project_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;hypothesis&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;project_goal_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;tags&#x22;" type="&#x22;list[str] | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;extra&#x22;" type="&#x22;Any&#x22;" value="&#x22;{}&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;get_experiment&#x22;" type="&#x22;(self, experiment_id) -> dict | None&#x22;">
  <PySourceCode>
    ```python
    def get_experiment(self, experiment_id: str) -> dict | None:
        return self._call("get_experiment", experiment_id=experiment_id)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;experiment_id&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;dict | None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;update_experiment&#x22;" type="&#x22;(self, experiment_id, **patch) -> dict&#x22;">
  <PySourceCode>
    ```python
    def update_experiment(self, experiment_id: str, **patch: Any) -> dict:
        return self._call("update_experiment", experiment_id=experiment_id, patch=patch)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;experiment_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;patch&#x22;" type="&#x22;Any&#x22;" value="&#x22;{}&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;set_conclusion&#x22;" type="&#x22;(self, experiment_id, conclusion) -> dict&#x22;">
  <PySourceCode>
    ```python
    def set_conclusion(self, experiment_id: str, conclusion: str) -> dict:
        return self.update_experiment(experiment_id, conclusion=conclusion)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;experiment_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;conclusion&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;invalidate_experiment&#x22;" type="&#x22;(self, experiment_id, *, reason=None) -> dict&#x22;">
  <PySourceCode>
    ```python
    def invalidate_experiment(self, experiment_id: str, *, reason: str | None = None) -> dict:
        patch: dict[str, Any] = {"is_valid": False}
        if reason:
            patch["error_message"] = reason
        return self.update_experiment(experiment_id, **patch)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;experiment_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;reason&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;list_experiments&#x22;" type="&#x22;(self, project_id=None, *, valid_only=False) -> list[dict]&#x22;">
  <PySourceCode>
    ```python
    def list_experiments(self, project_id: str | None = None, *, valid_only: bool = False) -> list[dict]:
        return self._call("list_experiments", project_id=self._project(project_id),
                          valid_only=valid_only)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;project_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;valid_only&#x22;" type="&#x22;bool&#x22;" value="&#x22;False&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;list[dict]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;delete_experiment&#x22;" type="&#x22;(self, experiment_id) -> Any&#x22;">
  <PySourceCode>
    ```python
    def delete_experiment(self, experiment_id: str) -> Any:
        return self._call("delete_experiment", experiment_id=experiment_id)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;experiment_id&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Any&#x22;" />
</PyFunction>

<PyFunction name="&#x22;experiment_summaries&#x22;" type="&#x22;(self, project_id=None, *, valid_only=False) -> list[dict]&#x22;">
  <PySourceCode>
    ```python
    def experiment_summaries(self, project_id: str | None = None, *, valid_only: bool = False) -> list[dict]:
        return self._call("experiment_summaries", project_id=self._project(project_id),
                          valid_only=valid_only)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;project_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;valid_only&#x22;" type="&#x22;bool&#x22;" value="&#x22;False&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;list[dict]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;experiment_detail&#x22;" type="&#x22;(self, experiment_id, *, include_metrics=False) -> dict&#x22;">
  <PySourceCode>
    ```python
    def experiment_detail(self, experiment_id: str, *, include_metrics: bool = False) -> dict:
        return self._call("experiment_detail", experiment_id=experiment_id,
                          include_metrics=include_metrics)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;experiment_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;include_metrics&#x22;" type="&#x22;bool&#x22;" value="&#x22;False&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;create_group&#x22;" type="&#x22;(self, experiment_id, name, *, description=None) -> dict&#x22;">
  <PySourceCode>
    ```python
    def create_group(self, experiment_id: str, name: str, *, description: str | None = None) -> dict:
        return self._call("create_group", experiment_id=experiment_id, name=name,
                          description=description)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;experiment_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;description&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;list_groups&#x22;" type="&#x22;(self, experiment_id) -> list[dict]&#x22;">
  <PySourceCode>
    ```python
    def list_groups(self, experiment_id: str) -> list[dict]:
        return self._call("list_groups", experiment_id=experiment_id)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;experiment_id&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;list[dict]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;create_run&#x22;" type="&#x22;(self, *, experiment_id, group_id=None, dataset_id=None, seed=None, recipe_kind=None, run_config=None, status='pending', **extra) -> dict&#x22;">
  <PySourceCode>
    ```python
    def create_run(self, *, experiment_id: str, group_id: str | None = None,
                   dataset_id: str | None = None, seed: int | None = None,
                   recipe_kind: str | None = None, run_config: dict | None = None,
                   status: str = "pending", **extra: Any) -> dict:
        return self._call("create_run", experiment_id=experiment_id, group_id=group_id,
                          dataset_id=dataset_id, seed=seed, recipe_kind=recipe_kind,
                          run_config=run_config, status=status, **extra)
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

    <PyParameter name="&#x22;status&#x22;" type="&#x22;str&#x22;" value="&#x22;'pending'&#x22;" />

    <PyParameter name="&#x22;extra&#x22;" type="&#x22;Any&#x22;" value="&#x22;{}&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;get_run&#x22;" type="&#x22;(self, run_id) -> dict | None&#x22;">
  <PySourceCode>
    ```python
    def get_run(self, run_id: str) -> dict | None:
        return self._call("get_run", run_id=run_id)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;dict | None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;update_run&#x22;" type="&#x22;(self, run_id, **patch) -> dict&#x22;">
  <PySourceCode>
    ```python
    def update_run(self, run_id: str, **patch: Any) -> dict:
        return self._call("update_run", run_id=run_id, patch=patch)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;patch&#x22;" type="&#x22;Any&#x22;" value="&#x22;{}&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;list_runs&#x22;" type="&#x22;(self, *, experiment_id=None, group_id=None) -> list[dict]&#x22;">
  <PySourceCode>
    ```python
    def list_runs(self, *, experiment_id: str | None = None, group_id: str | None = None) -> list[dict]:
        return self._call("list_runs", experiment_id=experiment_id, group_id=group_id)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;experiment_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;group_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;list[dict]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;create_dataset&#x22;" type="&#x22;(self, *, name, format, project_id=None, version=1, source_kind=None, transform=None, storage_uri=None, metadata=None) -> dict&#x22;">
  <PySourceCode>
    ```python
    def create_dataset(self, *, name: str, format: str, project_id: str | None = None,
                       version: int = 1, source_kind: str | None = None,
                       transform: list | None = None, storage_uri: str | None = None,
                       metadata: dict | None = None) -> dict:
        return self._call("create_dataset", project_id=self._project(project_id), name=name,
                          format=format, version=version, source_kind=source_kind,
                          transform=transform, storage_uri=storage_uri, metadata=metadata)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;format&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;project_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;version&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />

    <PyParameter name="&#x22;source_kind&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;transform&#x22;" type="&#x22;list | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;storage_uri&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;metadata&#x22;" type="&#x22;dict | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;add_dataset_rows&#x22;" type="&#x22;(self, dataset_id, rows, *, start_idx=0) -> list[dict]&#x22;">
  <PySourceCode>
    ```python
    def add_dataset_rows(self, dataset_id: str, rows: list[dict], *, start_idx: int = 0) -> list[dict]:
        return self._call("add_dataset_rows", dataset_id=dataset_id, rows=rows, start_idx=start_idx)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;dataset_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;rows&#x22;" type="&#x22;list[dict]&#x22;" value="null" />

    <PyParameter name="&#x22;start_idx&#x22;" type="&#x22;int&#x22;" value="&#x22;0&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;list[dict]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;get_dataset&#x22;" type="&#x22;(self, dataset_id) -> dict | None&#x22;">
  <PySourceCode>
    ```python
    def get_dataset(self, dataset_id: str) -> dict | None:
        return self._call("get_dataset", dataset_id=dataset_id)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;dataset_id&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;dict | None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;list_datasets&#x22;" type="&#x22;(self, project_id=None) -> list[dict]&#x22;">
  <PySourceCode>
    ```python
    def list_datasets(self, project_id: str | None = None) -> list[dict]:
        return self._call("list_datasets", project_id=self._project(project_id))
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;project_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;list[dict]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;get_dataset_rows&#x22;" type="&#x22;(self, dataset_id, *, limit=100, offset=0) -> list[dict]&#x22;">
  <PySourceCode>
    ```python
    def get_dataset_rows(self, dataset_id: str, *, limit: int = 100, offset: int = 0) -> list[dict]:
        return self._call("get_dataset_rows", dataset_id=dataset_id, limit=limit, offset=offset)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;dataset_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;limit&#x22;" type="&#x22;int&#x22;" value="&#x22;100&#x22;" />

    <PyParameter name="&#x22;offset&#x22;" type="&#x22;int&#x22;" value="&#x22;0&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;list[dict]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;create_benchmark&#x22;" type="&#x22;(self, *, name, format, project_id=None, version=1, source_kind=None, transform=None, storage_uri=None, metadata=None) -> dict&#x22;">
  <PySourceCode>
    ```python
    def create_benchmark(self, *, name: str, format: str, project_id: str | None = None,
                        version: int = 1, source_kind: str | None = None,
                        transform: list | None = None, storage_uri: str | None = None,
                        metadata: dict | None = None) -> dict:
        return self._call("create_benchmark", project_id=self._project(project_id), name=name,
                          format=format, version=version, source_kind=source_kind,
                          transform=transform, storage_uri=storage_uri, metadata=metadata)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;format&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;project_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;version&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;" />

    <PyParameter name="&#x22;source_kind&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;transform&#x22;" type="&#x22;list | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;storage_uri&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;metadata&#x22;" type="&#x22;dict | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;add_benchmark_rows&#x22;" type="&#x22;(self, benchmark_id, rows, *, start_idx=0) -> list[dict]&#x22;">
  <PySourceCode>
    ```python
    def add_benchmark_rows(self, benchmark_id: str, rows: list[dict], *, start_idx: int = 0) -> list[dict]:
        return self._call("add_benchmark_rows", benchmark_id=benchmark_id, rows=rows, start_idx=start_idx)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;benchmark_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;rows&#x22;" type="&#x22;list[dict]&#x22;" value="null" />

    <PyParameter name="&#x22;start_idx&#x22;" type="&#x22;int&#x22;" value="&#x22;0&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;list[dict]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;get_benchmark&#x22;" type="&#x22;(self, benchmark_id) -> dict | None&#x22;">
  <PySourceCode>
    ```python
    def get_benchmark(self, benchmark_id: str) -> dict | None:
        return self._call("get_benchmark", benchmark_id=benchmark_id)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;benchmark_id&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;dict | None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;list_benchmarks&#x22;" type="&#x22;(self, project_id=None) -> list[dict]&#x22;">
  <PySourceCode>
    ```python
    def list_benchmarks(self, project_id: str | None = None) -> list[dict]:
        return self._call("list_benchmarks", project_id=self._project(project_id))
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;project_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;list[dict]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;get_benchmark_rows&#x22;" type="&#x22;(self, benchmark_id, *, limit=100, offset=0) -> list[dict]&#x22;">
  <PySourceCode>
    ```python
    def get_benchmark_rows(self, benchmark_id: str, *, limit: int = 100, offset: int = 0) -> list[dict]:
        return self._call("get_benchmark_rows", benchmark_id=benchmark_id, limit=limit, offset=offset)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;benchmark_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;limit&#x22;" type="&#x22;int&#x22;" value="&#x22;100&#x22;" />

    <PyParameter name="&#x22;offset&#x22;" type="&#x22;int&#x22;" value="&#x22;0&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;list[dict]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;add_checkpoint&#x22;" type="&#x22;(self, *, run_id, uri, label=None, step=None, base_model=None, is_final=False) -> dict&#x22;">
  <PySourceCode>
    ```python
    def add_checkpoint(self, *, run_id: str, uri: str, label: str | None = None,
                      step: int | None = None, base_model: str | None = None,
                      is_final: bool = False) -> dict:
        return self._call("add_checkpoint", run_id=run_id, uri=uri, label=label, step=step,
                          base_model=base_model, is_final=is_final)
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

<PyFunction name="&#x22;list_checkpoints&#x22;" type="&#x22;(self, run_id) -> list[dict]&#x22;">
  <PySourceCode>
    ```python
    def list_checkpoints(self, run_id: str) -> list[dict]:
        return self._call("list_checkpoints", run_id=run_id)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;list[dict]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;create_eval&#x22;" type="&#x22;(self, *, run_id, benchmark_id=None, checkpoint_id=None, model_ref=None, step=None, metrics=None, breakdowns=None, sdk_version=None) -> dict&#x22;">
  <PySourceCode>
    ```python
    def create_eval(self, *, run_id: str, benchmark_id: str | None = None,
                   checkpoint_id: str | None = None, model_ref: str | None = None,
                   step: int | None = None, metrics: dict | None = None,
                   breakdowns: dict | None = None, sdk_version: str | None = None) -> dict:
        return self._call("create_eval", run_id=run_id, benchmark_id=benchmark_id,
                          checkpoint_id=checkpoint_id, model_ref=model_ref, step=step,
                          metrics=metrics or {}, breakdowns=breakdowns, sdk_version=sdk_version)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;benchmark_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;checkpoint_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;model_ref&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;dict | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;breakdowns&#x22;" type="&#x22;dict | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;sdk_version&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;list_evals&#x22;" type="&#x22;(self, run_id) -> list[dict]&#x22;">
  <PySourceCode>
    ```python
    def list_evals(self, run_id: str) -> list[dict]:
        return self._call("list_evals", run_id=run_id)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;list[dict]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;add_prediction&#x22;" type="&#x22;(self, *, run_id, kind, eval_id=None, task_id=None, instruction=None, model_output=None, expected=None, reward=None, advantage=None, step=None, sample_idx=0, metadata=None) -> dict&#x22;">
  <PySourceCode>
    ```python
    def add_prediction(self, *, run_id: str, kind: str, eval_id: str | None = None,
                       task_id: str | None = None, instruction: str | None = None,
                       model_output: str | None = None, expected: Any = None,
                       reward: float | None = None, advantage: float | None = None,
                       step: int | None = None, sample_idx: int = 0,
                       metadata: dict | None = None) -> dict:
        return self._call("add_prediction", run_id=run_id, kind=kind, eval_id=eval_id,
                          task_id=task_id, instruction=instruction, model_output=model_output,
                          expected=expected, reward=reward, advantage=advantage, step=step,
                          sample_idx=sample_idx, metadata=metadata)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;kind&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;eval_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;task_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;instruction&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;model_output&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;expected&#x22;" type="&#x22;Any&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;reward&#x22;" type="&#x22;float | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;advantage&#x22;" type="&#x22;float | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;sample_idx&#x22;" type="&#x22;int&#x22;" value="&#x22;0&#x22;" />

    <PyParameter name="&#x22;metadata&#x22;" type="&#x22;dict | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;list_predictions&#x22;" type="&#x22;(self, run_id, *, kind=None) -> list[dict]&#x22;">
  <PySourceCode>
    ```python
    def list_predictions(self, run_id: str, *, kind: str | None = None) -> list[dict]:
        return self._call("list_predictions", run_id=run_id, kind=kind)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;kind&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;list[dict]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;log_metric&#x22;" type="&#x22;(self, *, run_id, step, name, value, split='train') -> dict&#x22;">
  <PySourceCode>
    ```python
    def log_metric(self, *, run_id: str, step: int, name: str, value: float,
                   split: str = "train") -> dict:
        return self._call("log_metric", run_id=run_id, step=step, name=name,
                          value=value, split=split)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int&#x22;" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;value&#x22;" type="&#x22;float&#x22;" value="null" />

    <PyParameter name="&#x22;split&#x22;" type="&#x22;str&#x22;" value="&#x22;'train'&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;dict&#x22;" />
</PyFunction>

<PyFunction name="&#x22;log_metrics&#x22;" type="&#x22;(self, *, run_id, step, metrics, split='train') -> list[dict]&#x22;">
  <PySourceCode>
    ```python
    def log_metrics(self, *, run_id: str, step: int, metrics: dict[str, float],
                    split: str = "train") -> list[dict]:
        return self._call("log_metrics", run_id=run_id, step=step, metrics=metrics, split=split)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int&#x22;" value="null" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="null" />

    <PyParameter name="&#x22;split&#x22;" type="&#x22;str&#x22;" value="&#x22;'train'&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;list[dict]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;get_metrics&#x22;" type="&#x22;(self, run_id, *, name=None, split=None) -> list[dict]&#x22;">
  <PySourceCode>
    ```python
    def get_metrics(self, run_id: str, *, name: str | None = None,
                    split: str | None = None) -> list[dict]:
        return self._call("get_metrics", run_id=run_id, name=name, split=split)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;split&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;list[dict]&#x22;" />
</PyFunction>
