# NoOpEnvironment (/docs/evsys_sdk/training/harbor_agents/NoOpEnvironment)



Sandbox-free environment: every operation is a no-op (no container).

Agents using this run in-process; there's nothing to upload/download, so the
log-sync steps harbor logs a warning for are harmless (the agent writes
directly onto the `AgentContext`, which is harvested regardless).

## Functions [#functions]

<PyFunction name="&#x22;type&#x22;" type="&#x22;() -> str&#x22;">
  <PySourceCode>
    ```python
    @staticmethod
    def type() -> str:
        return "noop"
    ```
  </PySourceCode>

  <PyFunctionReturn type="&#x22;str&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_validate_definition&#x22;" type="&#x22;(self) -> None&#x22;">
  <PySourceCode>
    ```python
    def _validate_definition(self) -> None:
        return None
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;start&#x22;" type="&#x22;(self, force_build=False) -> None&#x22;">
  <PySourceCode>
    ```python
    async def start(self, force_build: bool = False) -> None:
        return None
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;force_build&#x22;" type="&#x22;bool&#x22;" value="&#x22;False&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;stop&#x22;" type="&#x22;(self, delete=True) -> None&#x22;">
  <PySourceCode>
    ```python
    async def stop(self, delete: bool = True) -> None:
        return None
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;delete&#x22;" type="&#x22;bool&#x22;" value="&#x22;True&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;upload_file&#x22;" type="&#x22;(self, source_path, target_path) -> None&#x22;">
  <PySourceCode>
    ```python
    async def upload_file(self, source_path, target_path) -> None:
        return None
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;source_path&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;target_path&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;upload_dir&#x22;" type="&#x22;(self, source_dir, target_dir) -> None&#x22;">
  <PySourceCode>
    ```python
    async def upload_dir(self, source_dir, target_dir) -> None:
        return None
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;source_dir&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;target_dir&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;download_file&#x22;" type="&#x22;(self, source_path, target_path) -> None&#x22;">
  <PySourceCode>
    ```python
    async def download_file(self, source_path, target_path) -> None:
        return None
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;source_path&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;target_path&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;download_dir&#x22;" type="&#x22;(self, source_dir, target_dir) -> None&#x22;">
  <PySourceCode>
    ```python
    async def download_dir(self, source_dir, target_dir) -> None:
        return None
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;source_dir&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;target_dir&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;exec&#x22;" type="&#x22;(self, command, cwd=None, env=None, timeout_sec=None, user=None) -> ExecResult&#x22;">
  <PySourceCode>
    ```python
    async def exec(self, command, cwd=None, env=None, timeout_sec=None, user=None) -> ExecResult:
        return ExecResult(stdout="", stderr="", return_code=0)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;command&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;cwd&#x22;" type="null" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;env&#x22;" type="null" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;timeout_sec&#x22;" type="null" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;user&#x22;" type="null" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;harbor.environments.base.ExecResult&#x22;" />
</PyFunction>
