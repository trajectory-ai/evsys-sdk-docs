# evsys_sdk (/docs/evsys_sdk)



evsys\_sdk — declarative, modular LLM experiment framework.

Most researcher code only needs the OOP orchestration surface:

from evsys\_sdk import Experiment
Experiment.from\_yaml("config.yaml").run()

For everything else:

from evsys\_sdk import (

# OOP orchestration [#oop-orchestration]

Experiment, ExperimentResult, ArmResult, Sweep,
Benchmark, BenchmarkScore, Checkpoint,

# Config models [#config-models]

ExperimentConfig, RunConfig, AlgorithmConfig, DataConfig, ModelConfig,
BackendConfig, VerifierSpec,

# YAML [#yaml]

load\_yaml, dump\_yaml, validate\_yaml,

# Registries (decorators for extensions) [#registries-decorators-for-extensions]

register\_algorithm, register\_verifier, register\_metric,
register\_backend, register\_data\_store, register\_log\_store,
register\_inference, register\_transform,
get\_algorithm, get\_verifier, get\_metric,

# Imperative runner (kept for advanced use; Experiment is the default) [#imperative-runner-kept-for-advanced-use-experiment-is-the-default]

run\_experiment,
)

Built-in extensions live in subpackages and self-register on import.
External packages can extend any registry via Python entry points
(group: `evsys_sdk.\<plural>` — see docs/cookbook.md).

<PyAttribute name="&#x22;__version__&#x22;" type="null" value="&#x22;'0.1.0'&#x22;" />

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['__version__', 'AlgorithmConfig', 'BackendConfig', 'DataConfig', 'DataStoreSpec', 'CallbackSpec', 'ExperimentConfig', 'LogStoreSpec', 'ModelConfig', 'RunConfig', 'TransformSpec', 'VerifierSpec', 'Algorithm', 'Backend', 'DataStore', 'InferenceClient', 'LogStore', 'Metric', 'RunContext', 'RunResult', 'Transform', 'Verifier', 'get_algorithm', 'get_backend', 'get_callback', 'get_data_store', 'get_inference', 'get_log_store', 'get_metric', 'get_transform', 'get_verifier', 'list_algorithms', 'list_backends', 'list_callbacks', 'list_data_stores', 'list_inferences', 'list_log_stores', 'list_metrics', 'list_transforms', 'list_verifiers', 'register_algorithm', 'register_backend', 'register_callback', 'register_data_store', 'register_inference', 'register_log_store', 'register_metric', 'register_transform', 'register_verifier', 'run_experiment', 'dump_yaml', 'load_yaml', 'validate_yaml', 'TargetFormat', 'ChatMessagesRow', 'HarborTask', 'PromptExample', 'InProcessVerifier', 'E2BVerifier', 'LLMJudgeVerifier', 'VerifierPayload', 'text_block', 'image_url_block', 'image_base64_block', 'block_to_image_src', 'has_images', 'detect_format', 'harbor_task_from_dict', 'chat_messages_row_from_dict', 'prompt_example_from_dict', 'from_dict', 'parse_rows', 'to_dict', 'iter_jsonl', 'DashboardClient', 'DashboardClientError', 'EvsysAuthError', 'ExperimentRun', 'configure_logger', 'get_logger', 'set_level', 'EvsysStore', 'EvsysStoreError', 'Workspace', 'MaterializedDataset', 'ArmResult', 'Benchmark', 'run_benchmark', 'BenchmarkScore', 'BenchmarkTaskResult', 'Checkpoint', 'EvalResult', 'Experiment', 'ExperimentResult', 'Sweep', 'expand_runs', 'find_manifest', 'forward_step_metrics', 'read_manifest']&#x22;" />

<Tabs items="[&#x22;Modules&#x22;]">
  <Tab value="&#x22;Modules&#x22;">
    <Cards>
      <Card href="&#x22;/docs/evsys_sdk/store&#x22;" title="&#x22;store&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/runner&#x22;" title="&#x22;runner&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/step_metrics&#x22;" title="&#x22;step_metrics&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/config&#x22;" title="&#x22;config&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/benchmark&#x22;" title="&#x22;benchmark&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/checkpoint&#x22;" title="&#x22;checkpoint&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/protocols&#x22;" title="&#x22;protocols&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/registry&#x22;" title="&#x22;registry&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/constants&#x22;" title="&#x22;constants&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/local_store&#x22;" title="&#x22;local_store&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/benchmark_run&#x22;" title="&#x22;benchmark_run&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/logger&#x22;" title="&#x22;logger&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/data_types&#x22;" title="&#x22;data_types&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/project_init&#x22;" title="&#x22;project_init&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/new_experiment&#x22;" title="&#x22;new_experiment&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/experiment&#x22;" title="&#x22;experiment&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/cli&#x22;" title="&#x22;cli&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/dashboard_client&#x22;" title="&#x22;dashboard_client&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/sweep&#x22;" title="&#x22;sweep&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/yaml_loader&#x22;" title="&#x22;yaml_loader&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/workspace&#x22;" title="&#x22;workspace&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/_entry_points&#x22;" title="&#x22;_entry_points&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/benchmark_upload&#x22;" title="&#x22;benchmark_upload&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/_harbor_upload&#x22;" title="&#x22;_harbor_upload&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/metrics&#x22;" title="&#x22;metrics&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/data_stores&#x22;" title="&#x22;data_stores&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/training&#x22;" title="&#x22;training&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/backends&#x22;" title="&#x22;backends&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/algorithms&#x22;" title="&#x22;algorithms&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/verifiers&#x22;" title="&#x22;verifiers&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/transforms&#x22;" title="&#x22;transforms&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/log_stores&#x22;" title="&#x22;log_stores&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/inference&#x22;" title="&#x22;inference&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/eval&#x22;" title="&#x22;eval&#x22;" />
    </Cards>
  </Tab>
</Tabs>
