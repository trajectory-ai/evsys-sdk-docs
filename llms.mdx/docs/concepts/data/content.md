# Data (/docs/concepts/data)



The data surface standardizes *anything* into a few typed shapes, then hands
those to the algorithm. Tokenization and supervision live **below** this
boundary — the typed rows carry only data; the algorithm decides what's a
target token.

<Mermaid
  chart="flowchart LR
    subgraph SRC[&#x22;raw source (DataConfig)&#x22;]
        S1[&#x22;dataset_id / dataset_name&#x22;]
        S2[&#x22;jsonl · json · hf_dataset · in_memory&#x22;]
    end
    S1 --> WS[&#x22;Workspace → .evsys/ cache<br/>version-immutable lineage&#x22;]
    WS --> RAW[&#x22;raw rows (dicts)&#x22;]
    S2 --> RAW
    RAW --> TF[&#x22;transforms[] (Transform)<br/>e.g. jsonl_to_chat&#x22;]
    TF --> PR[&#x22;parse_rows(TargetFormat)<br/>strict typed boundary&#x22;]
    PR --> CM[&#x22;ChatMessagesRow&#x22;]
    PR --> PE[&#x22;PromptExample&#x22;]
    PR --> HT[&#x22;HarborTask&#x22;]
    CM & PE & HT --> ALGO[&#x22;→ algorithm&#x22;]"
/>

* **You define:** a `DataConfig` (source + `transforms`), and a custom
  `Transform` when the built-ins don't fit. You pick which typed format the
  algorithm consumes.
* **The SDK handles:** loading, pull/cache-by-id with lineage (`Workspace`),
  running transforms in order, and the strict `parse_rows` conversion.

| Class                                              | Implementable?                  | Contract                                                                      |
| -------------------------------------------------- | ------------------------------- | ----------------------------------------------------------------------------- |
| `DataConfig`                                       | author in YAML                  | source + `transforms[]`                                                       |
| **`Transform`**                                    | **yes** (`@register_transform`) | `__call__(rows) -> rows` + `Config`                                           |
| `ChatMessagesRow` / `PromptExample` / `HarborTask` | choose shape                    | **data only — no supervision encoded**                                        |
| `DataStore`                                        | rarely                          | `read_jsonl` / `write_jsonl` / `read_json` / `write_json` / `exists` / `list` |
| `Workspace` / `MaterializedDataset`                | no (SDK)                        | pull / cache / lineage                                                        |

<Callout title="The dataset-format rule">
  Data formats hold only data. **The algorithm decides which tokens are
  targets** — the same `ChatMessagesRow` can be used for SFT or as an RL prompt.
</Callout>

Next: [Algorithms](/docs/concepts/algorithms) ·
[data types reference](/docs/evsys_sdk/data_types).
