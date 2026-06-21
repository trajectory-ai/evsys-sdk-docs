# Workspace (/docs/evsys_sdk/workspace/Workspace)



## Attributes [#attributes]

<PyAttribute name="&#x22;store&#x22;" type="null" value="&#x22;store or EvsysStore()&#x22;" />

<PyAttribute name="&#x22;root&#x22;" type="null" value="&#x22;Path(root or os.environ.get(_WORKSPACE_ENV) or _DEFAULT_ROOT)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, store=None, *, root=None) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, store: EvsysStore | None = None, *, root: str | None = None) -> None:
        self.store = store or EvsysStore()
        self.root = Path(root or os.environ.get(_WORKSPACE_ENV) or _DEFAULT_ROOT)
        self.root.mkdir(parents=True, exist_ok=True)
        gi = self.root / ".gitignore"
        if not gi.exists():
            gi.write_text("*\n")  # self-ignoring: workspace is never tracked
        for sub in ("datasets", "benchmarks", "scripts", "outputs"):
            (self.root / sub).mkdir(exist_ok=True)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;store&#x22;" type="&#x22;EvsysStore | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;root&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;pull_dataset&#x22;" type="&#x22;(self, dataset_id, *, force=False) -> MaterializedDataset&#x22;">
  <PySourceCode>
    ```python
    def pull_dataset(self, dataset_id: str, *, force: bool = False) -> MaterializedDataset:
        return self._materialize(
            "datasets", dataset_id, force,
            get_meta=self.store.get_dataset,
            get_rows=self.store.get_dataset_rows,
        )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;dataset_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;force&#x22;" type="&#x22;bool&#x22;" value="&#x22;False&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.workspace.MaterializedDataset&#x22;" />
</PyFunction>

<PyFunction name="&#x22;pull_benchmark&#x22;" type="&#x22;(self, benchmark_id, *, force=False) -> MaterializedDataset&#x22;">
  <PySourceCode>
    ```python
    def pull_benchmark(self, benchmark_id: str, *, force: bool = False) -> MaterializedDataset:
        return self._materialize(
            "benchmarks", benchmark_id, force,
            get_meta=self.store.get_benchmark,
            get_rows=self.store.get_benchmark_rows,
        )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;benchmark_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;force&#x22;" type="&#x22;bool&#x22;" value="&#x22;False&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.workspace.MaterializedDataset&#x22;" />
</PyFunction>

<PyFunction name="&#x22;dataset_id_for_name&#x22;" type="&#x22;(self, name) -> str&#x22;">
  <PySourceCode>
    ```python
    def dataset_id_for_name(self, name: str) -> str:
        return self._id_for_name(self.store.list_datasets, name, "dataset")
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str&#x22;" />
</PyFunction>

<PyFunction name="&#x22;benchmark_id_for_name&#x22;" type="&#x22;(self, name) -> str&#x22;">
  <PySourceCode>
    ```python
    def benchmark_id_for_name(self, name: str) -> str:
        return self._id_for_name(self.store.list_benchmarks, name, "benchmark")
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_id_for_name&#x22;" type="&#x22;(self, list_fn, name, kind) -> str&#x22;">
  Resolve a name to the highest-version record's id for this project.

  <PySourceCode>
    ```python
    def _id_for_name(self, list_fn: Callable[[], list[dict] | None], name: str, kind: str) -> str:
        """Resolve a name to the highest-version record's id for this project."""
        records = list_fn() or []
        matching = [r for r in records if r.get("name") == name]
        if not matching:
            raise FileNotFoundError(f"no {kind} named {name!r} in this project")
        return str(max(matching, key=lambda r: int(r.get("version") or 1))["id"])
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;list_fn&#x22;" type="&#x22;Callable[[], list[dict] | None]&#x22;" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;kind&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_materialize&#x22;" type="&#x22;(self, sub, obj_id, force, *, get_meta, get_rows) -> MaterializedDataset&#x22;">
  <PySourceCode>
    ```python
    def _materialize(self, sub: str, obj_id: str, force: bool, *,
                     get_meta: Callable[[str], dict | None],
                     get_rows: Callable[..., list[dict]]) -> MaterializedDataset:
        meta = get_meta(obj_id)
        if meta is None:
            raise FileNotFoundError(f"{sub[:-1]} {obj_id} not found")
        n_rows = int(meta.get("n_rows") or 0)
        fmt, transform = meta.get("format"), meta.get("transform")

        path = self.root / sub / f"{obj_id}.jsonl"
        man_path = self.root / sub / f"{obj_id}.meta.json"

        if force:
            for p in (path, man_path):
                p.unlink(missing_ok=True)
        elif path.exists() and man_path.exists():
            try:
                man = json.loads(man_path.read_text())
                if man.get("complete") and int(man.get("n_rows", -1)) == n_rows:
                    return MaterializedDataset(str(path), fmt, transform, n_rows, cached=True)
            except Exception:
                pass  # corrupt manifest → re-pull

        # Pull pages → temp file → atomic rename (no half-written cache is trusted).
        tmp = self.root / sub / f".{obj_id}.tmp.jsonl"
        written = 0
        with tmp.open("w") as f:
            offset = 0
            while True:
                page = get_rows(obj_id, limit=_PAGE, offset=offset)
                if not page:
                    break
                for row in page:
                    f.write(json.dumps(row.get("payload", row), default=str) + "\n")
                    written += 1
                if len(page) < _PAGE:
                    break
                offset += _PAGE
        tmp.replace(path)
        man_path.write_text(json.dumps({
            "id": obj_id, "version": meta.get("version"), "n_rows": written,
            "format": fmt, "transform": transform, "complete": True,
        }))
        return MaterializedDataset(str(path), fmt, transform, written, cached=False)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;sub&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;obj_id&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;force&#x22;" type="&#x22;bool&#x22;" value="null" />

    <PyParameter name="&#x22;get_meta&#x22;" type="&#x22;Callable[[str], dict | None]&#x22;" value="null" />

    <PyParameter name="&#x22;get_rows&#x22;" type="&#x22;Callable[..., list[dict]]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.workspace.MaterializedDataset&#x22;" />
</PyFunction>

<PyFunction name="&#x22;script_path&#x22;" type="&#x22;(self, exp_id) -> str&#x22;">
  <PySourceCode>
    ```python
    def script_path(self, exp_id: str) -> str:
        return str(self.root / "scripts" / f"exp_{exp_id}.py")
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;exp_id&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str&#x22;" />
</PyFunction>

<PyFunction name="&#x22;outputs_dir&#x22;" type="&#x22;(self, run_id) -> str&#x22;">
  <PySourceCode>
    ```python
    def outputs_dir(self, run_id: str) -> str:
        d = self.root / "outputs" / str(run_id)
        d.mkdir(parents=True, exist_ok=True)
        return str(d)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;run_id&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;str&#x22;" />
</PyFunction>
