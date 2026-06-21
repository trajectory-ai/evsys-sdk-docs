# LocalSFT (/docs/evsys_sdk/algorithms/local_sft/LocalSFT)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'local_sft'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;LocalSFTConfig&#x22;" />

<PyAttribute name="&#x22;cfg&#x22;" type="null" value="&#x22;LocalSFTConfig.model_validate(kwargs)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, **kwargs) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, **kwargs) -> None:
        self.cfg = LocalSFTConfig.model_validate(kwargs)
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
        if ctx.backend.name != "local":
            raise RuntimeError(f"LocalSFT requires backend=local (got '{ctx.backend.name}')")

        handles = ctx.extras.get("backend_handles", {})
        model = handles.get("model")
        tokenizer = handles.get("tokenizer")
        if model is None or tokenizer is None:
            raise RuntimeError("LocalSFT.train: backend_handles missing 'model' or 'tokenizer'")
        rows = ctx.extras.get("train_rows")
        if not rows:
            raise RuntimeError("LocalSFT.train: ctx.extras['train_rows'] missing/empty")

        out = Path(ctx.output_dir)
        out.mkdir(parents=True, exist_ok=True)

        ctx.log_store.log_hyperparams({"algorithm": self.name, **self.cfg.model_dump()})

        train_ds = Dataset.from_list([{"messages": r["messages"]} for r in rows])
        lora_config = LoraConfig(
            r=self.cfg.lora_rank,
            lora_alpha=self.cfg.lora_alpha,
            lora_dropout=self.cfg.lora_dropout,
            target_modules=self.cfg.lora_target_modules,
            task_type="CAUSAL_LM",
        )

        sft_kwargs: dict = dict(
            output_dir=str(out),
            num_train_epochs=self.cfg.num_epochs,
            max_steps=self.cfg.max_steps if self.cfg.max_steps is not None else -1,
            per_device_train_batch_size=self.cfg.per_device_train_batch_size,
            gradient_accumulation_steps=self.cfg.gradient_accumulation_steps,
            learning_rate=self.cfg.learning_rate,
            warmup_steps=self.cfg.warmup_steps,
            max_length=self.cfg.max_seq_len,
            logging_steps=self.cfg.logging_steps,
            save_strategy="steps",
            save_steps=self.cfg.save_steps,
            save_total_limit=self.cfg.save_total_limit,
            bf16=self.cfg.bf16,
            fp16=self.cfg.fp16,
            report_to="none",
            seed=self.cfg.seed,
        )
        if self.cfg.max_steps is not None:
            sft_kwargs["max_steps"] = self.cfg.max_steps
        args = SFTConfig(**sft_kwargs)

        trainer = SFTTrainer(
            model=model,
            args=args,
            train_dataset=train_ds,
            peft_config=lora_config,
            processing_class=tokenizer,
        )

        try:
            trainer.train()
        except Exception as e:
            logger.exception("LocalSFT.train failed")
            return RunResult(run_id=ctx.run_id, status="failed", error=str(e))

        final = out / "final"
        trainer.save_model(str(final))
        tokenizer.save_pretrained(str(final))

        # Drain TRL log history into our log store.
        for entry in getattr(trainer.state, "log_history", []):
            step = int(entry.get("step", 0) or 0)
            metrics = {k: float(v) for k, v in entry.items() if isinstance(v, (int, float)) and k != "step"}
            if metrics:
                ctx.log_store.log_metrics(metrics, step=step)

        artifacts = {"final_checkpoint": str(final)}
        for ckpt in sorted(out.glob("checkpoint-*")):
            artifacts[ckpt.name] = str(ckpt)
        for k, v in artifacts.items():
            ctx.log_store.log_artifact(k, v, kind="checkpoint")

        loss_entries = [e for e in getattr(trainer.state, "log_history", []) if "loss" in e]
        final_loss = float(loss_entries[-1]["loss"]) if loss_entries else 0.0
        return RunResult(
            run_id=ctx.run_id,
            status="completed",
            metrics={"train/final_loss": final_loss},
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
