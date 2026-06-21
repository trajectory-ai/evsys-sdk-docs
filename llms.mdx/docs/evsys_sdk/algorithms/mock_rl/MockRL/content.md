# MockRL (/docs/evsys_sdk/algorithms/mock_rl/MockRL)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'mock_rl'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;MockRLConfig&#x22;" />

<PyAttribute name="&#x22;cfg&#x22;" type="null" value="&#x22;MockRLConfig.model_validate(kwargs)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, **kwargs) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, **kwargs) -> None:
        self.cfg = MockRLConfig.model_validate(kwargs)
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

        # Instantiate verifier just to prove the wiring works.
        v_cls = get_verifier(self.cfg.verifier_kind)
        verifier = v_cls(**self.cfg.verifier_params)

        artifacts: dict[str, str] = {}
        reward = 0.1
        for step in range(1, self.cfg.num_steps + 1):
            # Deterministic upward curve toward ~0.9, with plateau.
            reward = 0.9 - 0.8 * math.exp(-step / max(1, self.cfg.num_steps / 4))
            ctx.log_store.log_metrics(
                {"train/reward": reward, "train/kl": 0.0}, step=step
            )
            if step % max(1, self.cfg.save_every) == 0:
                ckpt = out / f"checkpoint-{step}"
                ckpt.mkdir(exist_ok=True)
                (ckpt / "metadata.json").write_text(
                    json.dumps({"step": step, "reward": reward})
                )
                artifacts[f"ckpt_step_{step}"] = str(ckpt)
                ctx.log_store.log_artifact(f"ckpt_step_{step}", str(ckpt), kind="checkpoint")

        final_dir = out / "final"
        final_dir.mkdir(exist_ok=True)
        (final_dir / "metadata.json").write_text(
            json.dumps({"final": True, "step": self.cfg.num_steps, "reward": reward})
        )
        artifacts["final_checkpoint"] = str(final_dir)
        ctx.log_store.log_artifact("final_checkpoint", str(final_dir), kind="checkpoint")

        # Probe the verifier so it's actually exercised.
        probe = verifier.verify(prompt="x", completion="<think>t</think>\n<answer>X</answer>", target={})
        return RunResult(
            run_id=ctx.run_id,
            status="completed",
            metrics={
                "train/final_reward": float(reward),
                "verifier/probe_reward": float(probe.reward),
            },
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
