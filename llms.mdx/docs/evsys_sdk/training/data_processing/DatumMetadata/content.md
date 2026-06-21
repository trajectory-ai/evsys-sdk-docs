# DatumMetadata (/docs/evsys_sdk/training/data_processing/DatumMetadata)



Per-Datum routing info kept on the side (not in the Datum itself —
tinker rejects unknown keys in loss\_fn\_inputs).

## Attributes [#attributes]

<PyAttribute name="&#x22;group_idx&#x22;" type="&#x22;int&#x22;" value="null">
  Index into the original `trajectory_groups` list — used for SDFT
  teacher-prompt lookup.
</PyAttribute>

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, group_idx) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;group_idx&#x22;" type="&#x22;int&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
