# Checkpoint (/docs/evsys_sdk/checkpoint/Checkpoint)



One row of a `checkpoints.jsonl` manifest.

## Attributes [#attributes]

<PyAttribute name="&#x22;label&#x22;" type="&#x22;str&#x22;" value="null">
  The `name` field — e.g. `"final"`, `"epoch-3"`, or a step string.
</PyAttribute>

<PyAttribute name="&#x22;step&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;">
  Training step / batch index, if recorded (`batch` in tinker).
</PyAttribute>

<PyAttribute name="&#x22;epoch&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;weights_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  Training-state checkpoint URI (`state_path` in tinker).
</PyAttribute>

<PyAttribute name="&#x22;sampler_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;">
  Inference-ready sampler URI; what you pass to a sampling client.
</PyAttribute>

<PyAttribute name="&#x22;raw&#x22;" type="&#x22;dict&#x22;" value="&#x22;field(default_factory=dict)&#x22;">
  Untouched manifest row, for fields not modeled above.
</PyAttribute>

<PyAttribute name="&#x22;has_path&#x22;" type="&#x22;bool&#x22;" value="null" />

## Functions [#functions]

<PyFunction name="&#x22;from_manifest_row&#x22;" type="&#x22;(cls, row) -> Checkpoint&#x22;">
  <PySourceCode>
    ```python
    @classmethod
    def from_manifest_row(cls, row: dict) -> Checkpoint:
        return cls(
            label=str(row.get("name", "?")),
            step=_as_int(row.get("batch")),
            epoch=_as_int(row.get("epoch")),
            weights_path=_as_str(row.get("state_path")),
            sampler_path=_as_str(row.get("sampler_path")),
            raw=dict(row),
        )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;cls&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;row&#x22;" type="&#x22;dict&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.checkpoint.Checkpoint&#x22;" />
</PyFunction>

<PyFunction name="&#x22;pick_final&#x22;" type="&#x22;(checkpoints) -> Checkpoint | None&#x22;">
  Pick the one to evaluate against.

  Strategy: prefer an explicit `name == "final"` row that exposes a
  path, else the last row that exposes a path, else None.

  <PySourceCode>
    ```python
    @staticmethod
    def pick_final(checkpoints: list["Checkpoint"]) -> Checkpoint | None:
        """Pick the one to evaluate against.

        Strategy: prefer an explicit ``name == "final"`` row that exposes a
        path, else the last row that exposes a path, else None.
        """
        candidates = [c for c in checkpoints if c.has_path]
        if not candidates:
            return None
        for c in candidates:
            if c.label == "final":
                return c
        return candidates[-1]
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;checkpoints&#x22;" type="&#x22;list['Checkpoint']&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.checkpoint.Checkpoint | None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, label, step=None, epoch=None, weights_path=None, sampler_path=None, raw=dict()) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;label&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;epoch&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;weights_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;sampler_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;raw&#x22;" type="&#x22;dict&#x22;" value="&#x22;dict()&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
