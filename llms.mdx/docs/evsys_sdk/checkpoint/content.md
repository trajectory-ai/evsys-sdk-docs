# checkpoint (/docs/evsys_sdk/checkpoint)



Checkpoint — parse the `checkpoints.jsonl` manifests algorithms write.

Tinker SFT/RL (and any other algorithm that follows the same convention)
appends one JSON row per saved checkpoint to `\<output_dir>/checkpoints.jsonl`:

\{"name": "final", "batch": 1520, "epoch": 10,
"state\_path": "tinker://...", "sampler\_path": "tinker://..."}

Researcher scripts repeatedly hand-roll a few lines to find this manifest,
parse it, and pick the right row to evaluate against. This module gives them:

* `Checkpoint` — one row, typed.
* `read_manifest(path)` — full ordered list.
* `find_manifest(run_dir)` — locate the manifest under a run directory.
* `Checkpoint.pick_final(checkpoints)` — pick the "evaluate me" row
  (prefer `name == "final"`, else the last row that exposes a path).

<PyAttribute name="&#x22;MANIFEST_NAME&#x22;" type="null" value="&#x22;'checkpoints.jsonl'&#x22;" />

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['Checkpoint', 'MANIFEST_NAME', 'find_manifest', 'read_manifest']&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;Checkpoint&#x22;" href="&#x22;/docs/evsys_sdk/checkpoint/Checkpoint&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;read_manifest&#x22;" type="&#x22;(path) -> list[Checkpoint]&#x22;">
      Parse a checkpoints.jsonl into ordered Checkpoint rows.

      Blank lines are skipped; malformed JSON raises `ValueError` (don't
      silently lose checkpoint pointers — the eval step depends on them).

      <PySourceCode>
        ```python
        def read_manifest(path: str | Path) -> list[Checkpoint]:
            """Parse a checkpoints.jsonl into ordered Checkpoint rows.

            Blank lines are skipped; malformed JSON raises ``ValueError`` (don't
            silently lose checkpoint pointers — the eval step depends on them).
            """
            p = Path(path)
            if not p.is_file():
                raise FileNotFoundError(f"checkpoint manifest not found: {p}")
            out: list[Checkpoint] = []
            for lineno, line in enumerate(p.read_text().splitlines(), 1):
                line = line.strip()
                if not line:
                    continue
                try:
                    row = json.loads(line)
                except json.JSONDecodeError as e:
                    raise ValueError(f"{p}:{lineno}: malformed jsonl: {e}") from e
                out.append(Checkpoint.from_manifest_row(row))
            return out
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;path&#x22;" type="&#x22;str | Path&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;list[evsys_sdk.checkpoint.Checkpoint]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;find_manifest&#x22;" type="&#x22;(run_dir) -> Path | None&#x22;">
      Locate `checkpoints.jsonl` under `run_dir` (recursive).

      Algorithms sometimes nest the manifest under a sub-directory
      (e.g. `\<run_dir>/\<sub>/checkpoints.jsonl`); search shallowest-first
      and return the first match. Returns `None` if no manifest exists.

      <PySourceCode>
        ```python
        def find_manifest(run_dir: str | Path) -> Path | None:
            """Locate `checkpoints.jsonl` under ``run_dir`` (recursive).

            Algorithms sometimes nest the manifest under a sub-directory
            (e.g. ``<run_dir>/<sub>/checkpoints.jsonl``); search shallowest-first
            and return the first match. Returns ``None`` if no manifest exists.
            """
            base = Path(run_dir)
            if not base.is_dir():
                return None
            # Sort by depth so the shallowest match wins.
            matches = sorted(base.rglob(MANIFEST_NAME), key=lambda p: len(p.parts))
            return matches[0] if matches else None
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;run_dir&#x22;" type="&#x22;str | Path&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;pathlib.Path | None&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_as_int&#x22;" type="&#x22;(v) -> int | None&#x22;">
      <PySourceCode>
        ```python
        def _as_int(v: Any) -> int | None:
            if v is None:
                return None
            try:
                return int(v)
            except (TypeError, ValueError):
                return None
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;v&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;int | None&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_as_str&#x22;" type="&#x22;(v) -> str | None&#x22;">
      <PySourceCode>
        ```python
        def _as_str(v: Any) -> str | None:
            if v is None:
                return None
            s = str(v)
            return s if s else None
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;v&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;str | None&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
