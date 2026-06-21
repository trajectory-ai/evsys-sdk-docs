# ManifestRow (/docs/evsys_sdk/training/checkpoints/ManifestRow)



## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

<PyAttribute name="&#x22;batch&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;epoch&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;state_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

<PyAttribute name="&#x22;sampler_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;to_json&#x22;" type="&#x22;(self) -> str&#x22;">
  <PySourceCode>
    ```python
    def to_json(self) -> str:
        d: dict[str, Any] = {"name": self.name}
        if self.batch is not None:
            d["batch"] = self.batch
        if self.epoch is not None:
            d["epoch"] = self.epoch
        if self.state_path:
            d["state_path"] = self.state_path
        if self.sampler_path:
            d["sampler_path"] = self.sampler_path
        return json.dumps(d, ensure_ascii=False)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str&#x22;" />
</PyFunction>

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, name, batch=None, epoch=None, state_path=None, sampler_path=None) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;batch&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;epoch&#x22;" type="&#x22;int | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;state_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;sampler_path&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
