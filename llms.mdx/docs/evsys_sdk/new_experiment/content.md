# new_experiment (/docs/evsys_sdk/new_experiment)



Scaffold a new experiment dir under `experiments/\<yyyymmdd>_\<slug>/`.

Emits two files:

* `config.yaml` — a minimal, runnable `ExperimentConfig` skeleton with
  placeholders for the model, data, algorithm, and a metadata block where
  hypothesis / success\_metric / benchmark live.
* `run.py` — the 3-line declarative entrypoint researchers run:

  from evsys\_sdk import Experiment
  import src   # registers project verifiers / metrics / transforms

  Experiment.from\_yaml("config.yaml").run()

Use via the CLI: `evsys new-experiment \<slug>`. Programmatically:
`new_experiment(project_root, slug)`.

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['new_experiment']&#x22;" />

<Tabs items="[&#x22;Functions&#x22;]">
  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;new_experiment&#x22;" type="&#x22;(project_root, slug, *, today=None) -> Path&#x22;">
      Create `\<project_root>/experiments/\<yyyymmdd>_\<slug>/` and write
      `config.yaml` + `run.py` inside. Returns the experiment dir.

      Refuses if the dir already exists — researchers can pick a different
      slug or delete the prior one.

      <PySourceCode>
        ```python
        def new_experiment(
            project_root: str | Path,
            slug: str,
            *,
            today: date | None = None,
        ) -> Path:
            """Create ``<project_root>/experiments/<yyyymmdd>_<slug>/`` and write
            ``config.yaml`` + ``run.py`` inside. Returns the experiment dir.

            Refuses if the dir already exists — researchers can pick a different
            slug or delete the prior one.
            """
            project_root = Path(project_root).expanduser().resolve()
            safe_slug = _normalize_slug(slug)
            today_str = (today or date.today()).strftime("%Y%m%d")
            dir_name = f"{today_str}_{safe_slug}"

            experiments_root = project_root / "experiments"
            experiments_root.mkdir(parents=True, exist_ok=True)

            exp_dir = experiments_root / dir_name
            if exp_dir.exists():
                raise FileExistsError(
                    f"{exp_dir} already exists — pick a different slug or remove the prior one"
                )
            exp_dir.mkdir()

            (exp_dir / "config.yaml").write_text(_config_yaml(safe_slug))
            (exp_dir / "run.py").write_text(_run_py())

            return exp_dir
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;project_root&#x22;" type="&#x22;str | Path&#x22;" value="null" />

        <PyParameter name="&#x22;slug&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;today&#x22;" type="&#x22;date | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="&#x22;pathlib.Path&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_normalize_slug&#x22;" type="&#x22;(slug) -> str&#x22;">
      Sanitize into `[a-z0-9_-]+`.

      <PySourceCode>
        ```python
        def _normalize_slug(slug: str) -> str:
            """Sanitize into ``[a-z0-9_-]+``."""
            cleaned = slug.strip().lower().replace(" ", "_")
            cleaned = _SLUG_INVALID_RE.sub("", cleaned)
            if not cleaned:
                raise ValueError(f"slug {slug!r} reduces to empty after normalization")
            return cleaned
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;slug&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;str&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_config_yaml&#x22;" type="&#x22;(slug) -> str&#x22;">
      <PySourceCode>
        ```python
        def _config_yaml(slug: str) -> str:
            return f"""\
        # Experiment config — read by `Experiment.from_yaml('config.yaml').run()`.
        # Fields under `metadata` drive Experiment-level behavior (hypothesis,
        # tags, success_metric, benchmark). The rest is a regular ExperimentConfig.

        name: {slug}
        output_dir: ./.evsys/outputs/{slug}

        metadata:
          hypothesis: "TODO: one-line claim this experiment is testing"
          tags: []
          # success_metric: pass_rate          # ranks arms; sets experiment.best_score
          # benchmark:
          #   path: data/benchmark/<name>      # local harbor dir to score against
          #   id: <dashboard-benchmark-id>     # paste from `evsys benchmark upload`
          #   breakdown_keys: [toolkit]
          #   max_tokens: 512

        # One run, or a `matrix:` sweep — see docs/DESIGN.md for the full schema.
        run:
          name: {slug}
          seed: 42
          data:
            source_kind: jsonl
            path: data/datasets/<name>/v1/train.jsonl
            transforms: []
          model:
            name: Qwen/Qwen3-4B
          algorithm:
            kind: local_sft
            params:
              learning_rate: 1.0e-4
              num_epochs: 1
              batch_size: 8
          backend:
            kind: mock
        """
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;slug&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;str&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_run_py&#x22;" type="&#x22;() -> str&#x22;">
      <PySourceCode>
        ```python
        def _run_py() -> str:
            return '''\
        """Entrypoint for this experiment.

        This file is intentionally tiny: all knobs live in config.yaml, all
        project-specific verifiers / metrics / transforms live in src/.
        """
        from __future__ import annotations

        from pathlib import Path

        from evsys_sdk import Experiment
        import src  # noqa: F401 — registers project verifiers / metrics / transforms


        if __name__ == "__main__":
            Experiment.from_yaml(Path(__file__).with_name("config.yaml")).run()
        '''
        ```
      </PySourceCode>

      <PyFunctionReturn type="&#x22;str&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
