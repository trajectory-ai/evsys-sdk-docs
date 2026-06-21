# ContinualConfig (/docs/evsys_sdk/config/ContinualConfig)



Continual-learning stages over a single base `run`.

Each entry in `datasets` becomes one training stage: the base `run` is
copied with its `data` replaced by that entry, trained in order, and each
stage starts from the previous stage's weights (fresh optimizer). All stages
run inside one experiment and are scored on all configured benchmarks.

Example:
run:
data: \{...}            # ignored; the per-stage data below is used
model: \{...}
algorithm: \{kind: sft, ...}
continual:
datasets:

* \{dataset\_name: corpus\_a, transforms: \[...]}
* \{dataset\_name: corpus\_b, transforms: \[...]}
* \{dataset\_name: corpus\_c, transforms: \[...]}

## Attributes [#attributes]

<PyAttribute name="&#x22;datasets&#x22;" type="&#x22;list[DataConfig]&#x22;" value="&#x22;Field(min_length=1)&#x22;" />

<PyAttribute name="&#x22;name_template&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  Optional stage-name template; uses \{base} and \{i}. Default '\{base}\_stage\{i}'.
</PyAttribute>
