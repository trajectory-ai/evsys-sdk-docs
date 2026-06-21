# MatrixSpec (/docs/evsys_sdk/config/MatrixSpec)



Cartesian-product expansion of a base run.

Example:
matrix:
base\_run:
...                # full RunConfig template
axes:
algorithm.params.lora\_rank: \[1, 8]
algorithm.params.learning\_rate: \[1e-4, 5e-5]
name\_template: "\{base}\_\_rank\{algorithm.params.lora\_rank}\_\_lr\{algorithm.params.learning\_rate}"

## Attributes [#attributes]

<PyAttribute name="&#x22;base_run&#x22;" type="&#x22;RunConfig&#x22;" value="null" />

<PyAttribute name="&#x22;axes&#x22;" type="&#x22;dict[str, list[Any]]&#x22;" value="&#x22;Field(default_factory=dict)&#x22;" />

<PyAttribute name="&#x22;name_template&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  Optional template for run names. Uses \{key} for axis values + \{base}.
</PyAttribute>
