# MockSFT (/docs/evsys_sdk/algorithms/mock_sft/MockSFT)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'mock_sft'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;MockSFTConfig&#x22;" />

<PyAttribute name="&#x22;cfg&#x22;" type="null" value="&#x22;MockSFTConfig.model_validate(kwargs)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, **kwargs) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, **kwargs) -> None:
        self.cfg = MockSFTConfig.model_validate(kwargs)
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
        out = Path(ctx.output_dir)
        out.mkdir(parents=True, exist_ok=True)

        ctx.log_store.log_hyperparams({"algorithm": self.name, **self.cfg.model_dump()})

        # Pull dataset to set step count.
        # Try the data spec to estimate; otherwise default 100.
        n_rows = ctx.extras.get("n_train_rows", 100)
        steps_per_epoch = max(1, n_rows // self.cfg.batch_size)
        total_steps = (
            self.cfg.max_steps
            if self.cfg.max_steps is not None
            else steps_per_epoch * self.cfg.num_epochs
        )

        save_steps = sorted({max(1, int(round(f * total_steps))) for f in self.cfg.save_at_fractions})

        artifacts: dict[str, str] = {}
        for step in range(1, total_steps + 1):
            # Loss decreases like 2.0 * exp(-step/total*3) + small noise; deterministic.
            loss = 2.0 * math.exp(-3.0 * step / total_steps) + 0.05 * math.sin(step * 0.3)
            ctx.log_store.log_metrics({"train/loss": loss}, step=step)
            if step in save_steps:
                ckpt_dir = out / f"checkpoint-{step}"
                ckpt_dir.mkdir(exist_ok=True)
                (ckpt_dir / "metadata.json").write_text(
                    json.dumps({"step": step, "loss": loss, "algorithm": self.name})
                )
                key = f"ckpt_step_{step}"
                ctx.log_store.log_artifact(key, str(ckpt_dir), kind="checkpoint")
                artifacts[key] = str(ckpt_dir)

        final_dir = out / "final"
        final_dir.mkdir(exist_ok=True)
        (final_dir / "metadata.json").write_text(
            json.dumps({"final": True, "step": total_steps, "algorithm": self.name})
        )
        artifacts["final_checkpoint"] = str(final_dir)
        ctx.log_store.log_artifact("final_checkpoint", str(final_dir), kind="checkpoint")

        return RunResult(
            run_id=ctx.run_id,
            status="completed",
            metrics={"train/final_loss": float(loss), "total_steps": float(total_steps)},
            artifacts=artifacts,
        )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;RunContext&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.protocols.RunResult&#x22;" />
</PyFunction>
