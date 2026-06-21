# GEPAPromptAlgorithm (/docs/evsys_sdk/algorithms/gepa_prompt/GEPAPromptAlgorithm)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'gepa_prompt'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;GEPAPromptConfig&#x22;" />

<PyAttribute name="&#x22;cfg&#x22;" type="null" value="&#x22;GEPAPromptConfig.model_validate(kwargs)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, **kwargs) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, **kwargs) -> None:
        self.cfg = GEPAPromptConfig.model_validate(kwargs)
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

        examples = ctx.extras.get("prompt_examples") or []
        if not examples:
            return RunResult(
                run_id=ctx.run_id, status="failed",
                error="GEPA requires ctx.extras['prompt_examples'] = list[PromptExample-like dict]",
            )

        score_fn = ctx.extras.get("prompt_score_fn") or _default_score_fn
        rng = random.Random(self.cfg.seed)

        task_lm = get_inference(self.cfg.task_lm)(**self.cfg.task_lm_config)
        ctx.log_store.log_hyperparams({"algorithm": self.name, **self.cfg.model_dump()})

        if self.cfg.use_gepa_lib:
            try:
                import gepa  # noqa: F401
                return _run_with_gepa_lib(
                    ctx=ctx, cfg=self.cfg, examples=examples,
                    task_lm=task_lm, score_fn=score_fn, out=out, rng=rng,
                )
            except ImportError:
                logger.info("gepa package not installed — using built-in hill-climber")

        return _run_hill_climber(
            ctx=ctx, cfg=self.cfg, examples=examples,
            task_lm=task_lm, score_fn=score_fn, out=out, rng=rng,
        )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;ctx&#x22;" type="&#x22;RunContext&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.protocols.RunResult&#x22;" />
</PyFunction>
