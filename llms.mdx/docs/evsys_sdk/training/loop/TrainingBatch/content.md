# TrainingBatch (/docs/evsys_sdk/training/loop/TrainingBatch)



One step's worth of training data + loss spec.

Constructed by a :class:`StepBuilder` per step. The loop hands `data`
to the backend along with `loss_fn` and `loss_fn_config`.

## Attributes [#attributes]

<PyAttribute name="&#x22;data&#x22;" type="&#x22;list[tinker.Datum]&#x22;" value="null" />

<PyAttribute name="&#x22;loss_fn&#x22;" type="&#x22;tinker.types.LossFnType | LossCallable&#x22;" value="null">
  Either a tinker-recognised string name (`"cross_entropy"` /
  `"importance_sampling"` / ...) for a server-side loss, OR a Python
  callable for a client-side custom loss. The loop dispatches the right
  backend method based on the type.
</PyAttribute>

<PyAttribute name="&#x22;loss_fn_config&#x22;" type="&#x22;dict[str, Any] | None&#x22;" value="&#x22;None&#x22;">
  Only used when `loss_fn` is a string.
</PyAttribute>

<PyAttribute name="&#x22;metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="&#x22;field(default_factory=dict)&#x22;">
  Algorithm-precomputed per-step metrics (e.g. teacher entropy,
  reward stats). Merged into the per-step log row.
</PyAttribute>

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, data, loss_fn, loss_fn_config=None, metrics=dict()) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;data&#x22;" type="&#x22;list[tinker.Datum]&#x22;" value="null" />

    <PyParameter name="&#x22;loss_fn&#x22;" type="&#x22;tinker.types.LossFnType | LossCallable&#x22;" value="null" />

    <PyParameter name="&#x22;loss_fn_config&#x22;" type="&#x22;dict[str, Any] | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;metrics&#x22;" type="&#x22;dict[str, float]&#x22;" value="&#x22;dict()&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
