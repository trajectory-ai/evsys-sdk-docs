# ComboAlgorithm (/docs/evsys_sdk/algorithms/combo/ComboAlgorithm)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'combo'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;ComboConfig&#x22;" />

<PyAttribute name="&#x22;cfg&#x22;" type="null" value="&#x22;ComboConfig.model_validate(kwargs)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, **kwargs) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, **kwargs) -> None:
        self.cfg = ComboConfig.model_validate(kwargs)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;kwargs&#x22;" type="null" value="&#x22;{}&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;train&#x22;" type="&#x22;(self, ctx) -> RunResult&#x22;">
  <PySourceCode>
    ```python
    def train(self, ctx: RunContext) -> RunResult:
        out_root = Path(ctx.output_dir)
        out_root.mkdir(parents=True, exist_ok=True)

        ctx.log_store.log_hyperparams({
            "algorithm": self.name,
            "n_phases":  len(self.cfg.phases),
            "phases":    [p.kind for p in self.cfg.phases],
        })

        last_artifacts: dict[str, str] = {}
        last_metrics: dict[str, float] = {}
        last_result: RunResult | None = None

        for i, phase in enumerate(self.cfg.phases, start=1):
            label = phase.name or f"phase{i}_{phase.kind}"
            phase_dir = out_root / label
            phase_dir.mkdir(exist_ok=True)

            logger.info("combo: starting %s (kind=%s)", label, phase.kind)

            try:
                algo_cls = get_algorithm(phase.kind)
                algo = algo_cls(**phase.config)
            except (KeyError, TypeError, ValueError) as e:
                msg = f"phase {label} could not be constructed: {e}"
                logger.error(msg)
                if self.cfg.fail_fast:
                    return RunResult(
                        run_id=ctx.run_id, status="failed",
                        metrics=last_metrics, artifacts=last_artifacts,
                        error=msg,
                    )
                continue

            sub_ctx = _phase_context(ctx, phase_dir, last_artifacts, label)
            try:
                result = algo.train(sub_ctx)
            except Exception as e:
                msg = f"phase {label} crashed: {e}"
                logger.exception(msg)
                if self.cfg.fail_fast:
                    return RunResult(
                        run_id=ctx.run_id, status="failed",
                        metrics=last_metrics, artifacts=last_artifacts,
                        error=msg,
                    )
                continue

            # Namespace metrics + artifacts by phase to avoid collisions.
            last_metrics.update({f"{label}/{k}": v for k, v in result.metrics.items()})
            last_artifacts.update({f"{label}/{k}": v for k, v in result.artifacts.items()})
            last_result = result

            if result.status != "completed":
                msg = f"phase {label} returned status={result.status} ({result.error})"
                logger.warning(msg)
                if self.cfg.fail_fast:
                    return RunResult(
                        run_id=ctx.run_id, status="failed",
                        metrics=last_metrics, artifacts=last_artifacts,
                        error=msg,
                    )

        return RunResult(
            run_id=ctx.run_id,
            status="completed" if last_result and last_result.status == "completed" else "failed",
            metrics=last_metrics,
            artifacts=last_artifacts,
        )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;RunContext&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.protocols.RunResult&#x22;" />
</PyFunction>
