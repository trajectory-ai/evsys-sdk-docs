# Sweep (/docs/evsys_sdk/sweep/Sweep)



Declarative axes over a single `RunConfig`.

## Attributes [#attributes]

<PyAttribute name="&#x22;base_run&#x22;" type="&#x22;RunConfig&#x22;" value="null" />

<PyAttribute name="&#x22;axes&#x22;" type="&#x22;dict[str, list[Any]]&#x22;" value="&#x22;field(default_factory=dict)&#x22;" />

<PyAttribute name="&#x22;name_template&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__post_init__&#x22;" type="&#x22;(self) -> None&#x22;">
  <PySourceCode>
    ```python
    def __post_init__(self) -> None:
        for axis, vals in self.axes.items():
            if not isinstance(vals, list) or len(vals) == 0:
                raise ValueError(
                    f"Sweep axis {axis!r} must be a non-empty list of values"
                )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;expand&#x22;" type="&#x22;(self) -> list[RunConfig]&#x22;">
  Cartesian product over axes → one `RunConfig` per cell.

  <PySourceCode>
    ```python
    def expand(self) -> list[RunConfig]:
        """Cartesian product over axes → one ``RunConfig`` per cell."""
        return expand_runs(self.base_run, self.axes, self.name_template)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;list[evsys_sdk.config.RunConfig]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;__len__&#x22;" type="&#x22;(self) -> int&#x22;">
  <PySourceCode>
    ```python
    def __len__(self) -> int:
        if not self.axes:
            return 1
        n = 1
        for vals in self.axes.values():
            n *= len(vals)
        return n
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;int&#x22;" />
</PyFunction>

<PyFunction name="&#x22;to_matrix&#x22;" type="&#x22;(self) -> MatrixSpec&#x22;">
  Repackage as a `MatrixSpec` for YAML serialization.

  <PySourceCode>
    ```python
    def to_matrix(self) -> MatrixSpec:
        """Repackage as a ``MatrixSpec`` for YAML serialization."""
        return MatrixSpec(
            base_run=self.base_run,
            axes=dict(self.axes),
            name_template=self.name_template,
        )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.config.MatrixSpec&#x22;" />
</PyFunction>

<PyFunction name="&#x22;from_matrix&#x22;" type="&#x22;(cls, matrix) -> Sweep&#x22;">
  <PySourceCode>
    ```python
    @classmethod
    def from_matrix(cls, matrix: MatrixSpec) -> Sweep:
        return cls(
            base_run=matrix.base_run,
            axes=dict(matrix.axes),
            name_template=matrix.name_template,
        )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;cls&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;matrix&#x22;" type="&#x22;MatrixSpec&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.sweep.Sweep&#x22;" />
</PyFunction>

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, base_run, axes=dict(), name_template=None) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;base_run&#x22;" type="&#x22;RunConfig&#x22;" value="null" />

    <PyParameter name="&#x22;axes&#x22;" type="&#x22;dict[str, list[Any]]&#x22;" value="&#x22;dict()&#x22;" />

    <PyParameter name="&#x22;name_template&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
