# Experiments (/docs/concepts/experiments)



The **Experiment** is the scientific container: a **hypothesis**, one or more
**training runs**, and an auto-synthesized **conclusion**. It's the top of the
spine — `config.yaml → Experiment → arms → backend → eval → artifacts`.

```python
from evsys_sdk import Experiment

Experiment.from_yaml("config.yaml").run()
```

<Mermaid
  chart="flowchart TD
    EX[&#x22;Experiment<br/>metadata: hypothesis · tags · success_metric · benchmark&#x22;]
    EX --> M{&#x22;run / runs / matrix&#x22;}
    M --> A1[&#x22;arm = RunConfig (a 'cell')&#x22;]
    M --> A2[&#x22;arm = RunConfig&#x22;]
    A1 -->|&#x22;n_repeats > 1&#x22;| G1[&#x22;group: seeded replicates<br/>seeds [base, base+1, …] · shared group_id&#x22;]
    A1 --> R1[&#x22;ArmResult<br/>metrics · eval_metrics · status&#x22;]
    A2 --> R2[&#x22;ArmResult&#x22;]
    G1 --> R1
    R1 & R2 --> PICK[&#x22;pick best by success_metric&#x22;]
    PICK --> ER[&#x22;ExperimentResult<br/>best_arm · best_score · conclusion&#x22;]"
/>

## Vocabulary [#vocabulary]

* **Experiment** — the top-level study. `Experiment.from_yaml(...).run()` owns
  dashboard experiment/run creation, sweep expansion, per-arm failure
  isolation, post-train scoring, metric forwarding, and conclusion building.
* **Training run (arm)** — one `RunConfig` = one concrete training job (one
  cell of a sweep). `runs` / `matrix` produce many arms.
* **Run group** — `n_repeats > 1` replicates an arm across seeds (shared
  `group_id`) so variance is a config field, not a bespoke script.
* **Hypothesis → success\_metric → conclusion** — the hypothesis is the
  question; `success_metric` ranks arms into `best_arm`; the `conclusion`
  summarizes the outcome. All recorded on the dashboard.

## Config objects [#config-objects]

| Object                           | Role                                                                                                                               |
| -------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `ExperimentConfig`               | top level: `name`, `output_dir`, stores, one of `run`/`runs`/`matrix`, `n_repeats`/`base_seed`, `parent_experiment_id`, `metadata` |
| `RunConfig`                      | one run: `data`, `model`, `algorithm`, `backend`, `eval`, `validation`, `seed`, `tags` — the cell the two surfaces plug into       |
| `MatrixSpec` / `Sweep`           | cartesian expansion over dotted-path axes → many `RunConfig`s via one `expand_runs()`                                              |
| `ExperimentResult` / `ArmResult` | outputs: per-arm metrics + the experiment-level `best_arm`, `conclusion`, `hypothesis`                                             |

<Callout title="Two runners">
  **`Experiment`*&#x2A; (OOP, with dashboard bookkeeping) wraps
  &#x2A;*`run_experiment(cfg)`** (the inner per-arm runner). Use `run_experiment`
  directly to train without bookkeeping.
</Callout>

Next: [Data](/docs/concepts/data) · [Algorithms](/docs/concepts/algorithms) ·
[full `ExperimentConfig` reference](/docs/evsys_sdk/config/ExperimentConfig).
