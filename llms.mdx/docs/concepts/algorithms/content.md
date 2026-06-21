# Algorithms & Evaluation (/docs/concepts/algorithms)



The algorithm surface is **one required contract** (`train(ctx) -> RunResult`)
plus an optional gradient toolkit and pluggable evaluation. It runs over any
tinker-compatible `Backend` — `mock` for tests, `local` for TRL+peft, `tinker`
for hosted.

<Mermaid
  chart="flowchart TD
    BK[&#x22;Backend.prepare/teardown<br/>mock · local · tinker&#x22;] --> CTX[&#x22;RunContext<br/>train_rows · backend_handles · log_store · output_dir&#x22;]
    CTX --> AL[&#x22;Algorithm.train(ctx) -> RunResult<br/>@register_algorithm&#x22;]

    subgraph L2[&#x22;optional gradient toolkit (Layer 2)&#x22;]
        SB[&#x22;StepBuilder.build_batch(n) -> TrainingBatch&#x22;]
        LP[&#x22;TrainingLoop<br/>fwd_bwd → optim → log → ckpt → eval&#x22;]
        LF[&#x22;loss: named str OR LossCallable&#x22;]
        SB --> LP
        LF --> LP
    end

    AL -. &#x22;may use&#x22; .-> SB
    AL --> RR[&#x22;RunResult<br/>status · metrics · artifacts&#x22;]

    subgraph EV[&#x22;Evaluation&#x22;]
        BMK[&#x22;Benchmark (test — scored once)&#x22;]
        VAL[&#x22;Validation (in-loop — every N steps)&#x22;]
        MET[&#x22;Metric.compute()&#x22;]
        VER[&#x22;Verifier.verify() / reward&#x22;]
    end
    AL --> EV
    BMK --> MET
    VAL --> MET
    EV --> VER"
/>

## Algorithm [#algorithm]

`Algorithm` (protocol): `name`, `Config`, `train(ctx) -> RunResult`. The only
required contract — `train()` may do anything. The optional toolkit:
`TrainingLoop` drives the gradient loop; a `StepBuilder` (`build_batch ->
TrainingBatch`) is the unit of "a new gradient method"; losses are a **named
string*&#x2A; or a &#x2A;*`LossCallable`** (client-side, via `forward_backward_custom`).

## Evaluation — a test/validation firewall [#evaluation--a-testvalidation-firewall]

* **Benchmark** — the *test set*, scored **once after** training. Model
  selection must never key off it.
* **Validation** — scored **in-loop** every N steps to drive selection.
* Both are harbor-format and scored via the **Metric** / **Verifier** registries.

## Metrics & Verifiers [#metrics--verifiers]

* **`Metric`**: `compute(predictions, targets) -> float`.
* **`Verifier`**: `verify(prompt, completion, target) -> reward` (RL reward or
  per-task scoring).
* **`InferenceClient`**: `generate(...)` — how eval/RL query a model.

<Callout type="warn" title="Keep the firewall intact">
  A `Benchmark` is your held-out test. If you select the best arm using
  benchmark scores, you've leaked the test set — use `Validation` for selection.
</Callout>

Next: [Extensibility](/docs/concepts/extensibility) ·
[algorithms API](/docs/evsys_sdk/algorithms).
