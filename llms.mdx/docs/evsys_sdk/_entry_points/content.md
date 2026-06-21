# _entry_points (/docs/evsys_sdk/_entry_points)



Load third-party extensions registered via Python entry points.

External packages declare extensions in their pyproject.toml:

\[project.entry-points."evsys\_sdk.algorithms"]
my\_dpo = "my\_pkg.algorithms:MyDPO"

When evsys\_sdk imports, we walk those groups and import each
target module — its top-level @register\_\* decorators run, populating our
registries. No fork required.

Failures here are non-fatal: a third-party package with an import error
shouldn't break the library. We log a warning and continue.

<PyAttribute name="&#x22;logger&#x22;" type="null" value="&#x22;logging.getLogger(__name__)&#x22;" />

<Tabs items="[&#x22;Functions&#x22;]">
  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;_load_group&#x22;" type="&#x22;(group) -> None&#x22;">
      <PySourceCode>
        ```python
        def _load_group(group: str) -> None:
            try:
                eps = entry_points(group=group)
            except Exception as e:  # importlib.metadata raises on weird envs
                logger.debug("entry_points lookup failed for %s: %s", group, e)
                return
            for ep in eps:
                try:
                    ep.load()
                except Exception as e:
                    logger.warning("Failed to load %s entry point %s: %s", group, ep.name, e)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;group&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;None&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
