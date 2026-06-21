# ExperimentConfig (/docs/evsys_sdk/config/ExperimentConfig)



Top-level experiment.

Either `run` (one run) or `runs` (multiple — a campaign) must be set.

The `matrix` shorthand expands into `runs` automatically: each axis
maps to a list of values, and the cartesian product is taken. Each cell
inherits everything from `base_run` and overrides the matrixed fields.

## Attributes [#attributes]

<PyAttribute name="&#x22;version&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;">
  Schema version. Bumped when breaking changes are made.
</PyAttribute>

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

<PyAttribute name="&#x22;description&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

<PyAttribute name="&#x22;output_dir&#x22;" type="&#x22;str&#x22;" value="&#x22;'./outputs'&#x22;">
  Where local artifacts (checkpoints, logs) are written.
</PyAttribute>

<PyAttribute name="&#x22;data_store&#x22;" type="&#x22;DataStoreSpec&#x22;" value="&#x22;Field(default_factory=DataStoreSpec)&#x22;" />

<PyAttribute name="&#x22;log_store&#x22;" type="&#x22;LogStoreSpec&#x22;" value="&#x22;Field(default_factory=LogStoreSpec)&#x22;" />

<PyAttribute name="&#x22;callbacks&#x22;" type="&#x22;list[CallbackSpec]&#x22;" value="&#x22;Field(default_factory=list)&#x22;" />

<PyAttribute name="&#x22;run&#x22;" type="&#x22;RunConfig | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;runs&#x22;" type="&#x22;list[RunConfig] | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;matrix&#x22;" type="&#x22;MatrixSpec | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;continual&#x22;" type="&#x22;ContinualConfig | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;n_repeats&#x22;" type="&#x22;int&#x22;" value="&#x22;1&#x22;">
  How many seeded replicates per primary RunConfig. 1 = no grouping.
</PyAttribute>

<PyAttribute name="&#x22;base_seed&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;">
  Starting seed for auto-generated replicates. None → use each primary's own `seed`.
</PyAttribute>

<PyAttribute name="&#x22;parent_experiment_id&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  For evolutionary lineage.
</PyAttribute>

<PyAttribute name="&#x22;metadata&#x22;" type="&#x22;dict[str, Any]&#x22;" value="&#x22;Field(default_factory=dict)&#x22;">
  Free-form (e.g. budget, hypothesis, client tag).
</PyAttribute>

## Functions [#functions]

<PyFunction name="&#x22;model_post_init&#x22;" type="&#x22;(self, __context) -> None&#x22;">
  <PySourceCode>
    ```python
    def model_post_init(self, __context: Any) -> None:
        n = sum(x is not None for x in (self.run, self.runs, self.matrix))
        if n != 1:
            raise ValueError(
                "ExperimentConfig must have exactly one of: run, runs, matrix "
                f"(found {n})."
            )
        if self.n_repeats < 1:
            raise ValueError(f"n_repeats must be >= 1 (got {self.n_repeats}).")
        if self.continual is not None and self.run is None:
            raise ValueError(
                "continual requires a single `run` as the base (not runs/matrix)."
            )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;__context&#x22;" type="&#x22;Any&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
