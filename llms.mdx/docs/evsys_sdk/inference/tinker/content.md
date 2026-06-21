# tinker (/docs/evsys_sdk/inference/tinker)



TinkerInference — generate via tinker SamplingClient.

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;TinkerInferenceConfig&#x22;" href="&#x22;/docs/evsys_sdk/inference/tinker/TinkerInferenceConfig&#x22;" />

      <Card title="&#x22;TinkerInference&#x22;" href="&#x22;/docs/evsys_sdk/inference/tinker/TinkerInference&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;_default_tinker_factory&#x22;" type="&#x22;(run_result, run_cfg) -> TinkerInference&#x22;">
      <PySourceCode>
        ```python
        @register_default_inference_factory("tinker")
        def _default_tinker_factory(run_result: Any, run_cfg: Any) -> TinkerInference:
            return TinkerInference.from_run_result(run_result, run_cfg)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;run_result&#x22;" type="&#x22;Any&#x22;" value="null" />

        <PyParameter name="&#x22;run_cfg&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;evsys_sdk.inference.tinker.TinkerInference&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
