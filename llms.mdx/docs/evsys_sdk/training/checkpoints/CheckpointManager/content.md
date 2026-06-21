# CheckpointManager (/docs/evsys_sdk/training/checkpoints/CheckpointManager)



Decide WHEN to save and WRITE the manifest row when we do.

The decision policy is intentionally tiny — `should_save(step)` returns
True every `save_every` steps (after the optimizer step at that index).
The actual save call is dispatched by the loop, which has the live
`Backend` handle; the manager only records the resulting paths.

Final-step save is unconditional: the loop calls `save_final(...)` after
its for-loop completes, so even when `save_every` doesn't land exactly
on the last step the final sampler is always recorded — that's the URI
downstream eval consumes.

## Attributes [#attributes]

<PyAttribute name="&#x22;log_path&#x22;" type="null" value="&#x22;Path(log_path)&#x22;" />

<PyAttribute name="&#x22;save_every&#x22;" type="null" value="&#x22;max(0, int(save_every))&#x22;" />

<PyAttribute name="&#x22;manifest_path&#x22;" type="null" value="&#x22;self.log_path / MANIFEST_NAME&#x22;" />

<PyAttribute name="&#x22;rows&#x22;" type="&#x22;list[ManifestRow]&#x22;" value="null" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, *, log_path, save_every) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(
        self,
        *,
        log_path: Path,
        save_every: int,
    ) -> None:
        self.log_path = Path(log_path)
        self.log_path.mkdir(parents=True, exist_ok=True)
        self.save_every = max(0, int(save_every))
        self.manifest_path = self.log_path / MANIFEST_NAME
        self._rows: list[ManifestRow] = []
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;log_path&#x22;" type="&#x22;Path&#x22;" value="null" />

    <PyParameter name="&#x22;save_every&#x22;" type="&#x22;int&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;should_save&#x22;" type="&#x22;(self, step) -> bool&#x22;">
  Save after the optimizer step at index `step` (zero-based).

  The convention matches tinker\_cookbook: `(step + 1) % save_every == 0`.
  Disabled when `save_every == 0`.

  <PySourceCode>
    ```python
    def should_save(self, step: int) -> bool:
        """Save after the optimizer step at index ``step`` (zero-based).

        The convention matches tinker_cookbook: ``(step + 1) % save_every == 0``.
        Disabled when ``save_every == 0``.
        """
        if self.save_every <= 0:
            return False
        return (step + 1) % self.save_every == 0
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;step&#x22;" type="&#x22;int&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;bool&#x22;" />
</PyFunction>

<PyFunction name="&#x22;record&#x22;" type="&#x22;(self, row) -> None&#x22;">
  Append one row to the manifest on disk and remember it.

  <PySourceCode>
    ```python
    def record(self, row: ManifestRow) -> None:
        """Append one row to the manifest on disk and remember it."""
        with self.manifest_path.open("a") as f:
            f.write(row.to_json() + "\n")
        self._rows.append(row)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;row&#x22;" type="&#x22;ManifestRow&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;find_resume&#x22;" type="&#x22;(self) -> Checkpoint | None&#x22;">
  Find the most-recent recorded checkpoint to resume training from.

  Reads the existing `checkpoints.jsonl` (if any) under `log_path` and
  picks the last row that has a `state_path`. The loop hands the
  state\_path back to the backend to recreate the training client with
  optimizer state intact.

  Returns `None` when no resumable checkpoint is on disk — caller
  should start fresh.

  <PySourceCode>
    ```python
    def find_resume(self) -> Checkpoint | None:
        """Find the most-recent recorded checkpoint to resume training from.

        Reads the existing `checkpoints.jsonl` (if any) under ``log_path`` and
        picks the last row that has a ``state_path``. The loop hands the
        state_path back to the backend to recreate the training client with
        optimizer state intact.

        Returns ``None`` when no resumable checkpoint is on disk — caller
        should start fresh.
        """
        if not self.manifest_path.is_file():
            return None
        try:
            ckpts = read_manifest(self.manifest_path)
        except Exception:
            logger.exception("CheckpointManager.find_resume: failed to parse %s",
                             self.manifest_path)
            return None
        for c in reversed(ckpts):
            if c.weights_path:
                return c
        return None
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.checkpoint.Checkpoint | None&#x22;" />
</PyFunction>
