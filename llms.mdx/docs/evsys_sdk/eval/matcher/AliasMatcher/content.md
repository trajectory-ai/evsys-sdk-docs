# AliasMatcher (/docs/evsys_sdk/eval/matcher/AliasMatcher)



Looks up whether `predicted` is an accepted answer for `expected`.

Inputs:

* primary\_aliases — flat map \{slug\_a: slug\_b}. Treated as a symmetric
  equivalence: `slug_a` and `slug_b` are accepted interchangeably.
* secondary\_aliases — \{slug\_a: \[slug\_b, slug\_c, ...]} for cases where
  multiple slugs are valid replacements (e.g. GOOGLESHEETS\_QUERY\_TABLE
  → \{BATCH\_GET, LOOKUP\_SPREADSHEET\_ROW, VALUES\_GET}). Also symmetric.
* bidirectional — set to False to disable reverse lookup (forward
  match only, like the original semantics).

## Attributes [#attributes]

<PyAttribute name="&#x22;primary_aliases&#x22;" type="&#x22;dict[str, str]&#x22;" value="&#x22;field(default_factory=dict)&#x22;" />

<PyAttribute name="&#x22;secondary_aliases&#x22;" type="&#x22;dict[str, list[str]]&#x22;" value="&#x22;field(default_factory=dict)&#x22;" />

<PyAttribute name="&#x22;bidirectional&#x22;" type="&#x22;bool&#x22;" value="&#x22;True&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__post_init__&#x22;" type="&#x22;(self) -> None&#x22;">
  <PySourceCode>
    ```python
    def __post_init__(self) -> None:
        self._build_equivalence()
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_build_equivalence&#x22;" type="&#x22;(self) -> None&#x22;">
  Build \{slug: \{all\_equivalents}} via union-find over alias pairs.

  <PySourceCode>
    ```python
    def _build_equivalence(self) -> None:
        """Build {slug: {all_equivalents}} via union-find over alias pairs."""
        parent: dict[str, str] = {}

        def find(x: str) -> str:
            parent.setdefault(x, x)
            while parent[x] != x:
                parent[x] = parent[parent[x]]
                x = parent[x]
            return x

        def union(a: str, b: str) -> None:
            ra, rb = find(a), find(b)
            if ra != rb:
                parent[ra] = rb

        for a, b in self.primary_aliases.items():
            if self.bidirectional:
                union(a, b)
            else:
                # Asymmetric: a -> b only; record a tagged class for a.
                find(a)
                find(b)
                parent.setdefault(a, find(b))

        for a, bs in self.secondary_aliases.items():
            for b in bs:
                if self.bidirectional:
                    union(a, b)
                else:
                    find(a)
                    find(b)
                    parent.setdefault(a, find(b))

        classes: dict[str, set[str]] = {}
        for slug in list(parent.keys()):
            root = find(slug)
            classes.setdefault(root, set()).add(slug)

        # Map each slug to its full equivalence class.
        eq: dict[str, set[str]] = {}
        for root, members in classes.items():
            for m in members:
                eq[m] = members
        self._equivalence = eq
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;from_files&#x22;" type="&#x22;(cls, primary_path, secondary_path=None, *, bidirectional=True) -> 'AliasMatcher'&#x22;">
  <PySourceCode>
    ```python
    @classmethod
    def from_files(
        cls,
        primary_path: str | Path,
        secondary_path: str | Path | None = None,
        *,
        bidirectional: bool = True,
    ) -> "AliasMatcher":
        primary = json.loads(Path(primary_path).read_text()) if primary_path else {}
        secondary = (
            json.loads(Path(secondary_path).read_text())
            if secondary_path and Path(secondary_path).exists()
            else {}
        )
        return cls(primary_aliases=primary, secondary_aliases=secondary, bidirectional=bidirectional)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;cls&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;primary_path&#x22;" type="&#x22;str | Path&#x22;" value="null" />

    <PyParameter name="&#x22;secondary_path&#x22;" type="&#x22;str | Path | None&#x22;" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;bidirectional&#x22;" type="&#x22;bool&#x22;" value="&#x22;True&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;'AliasMatcher'&#x22;" />
</PyFunction>

<PyFunction name="&#x22;accepted_slugs&#x22;" type="&#x22;(self, expected) -> list[str]&#x22;">
  All slugs that count as a correct prediction for `expected`.

  <PySourceCode>
    ```python
    def accepted_slugs(self, expected: str) -> list[str]:
        """All slugs that count as a correct prediction for ``expected``."""
        if expected in self._equivalence:
            # Preserve a stable order: expected first, then sorted rest.
            others = sorted(s for s in self._equivalence[expected] if s != expected)
            return [expected] + others
        return [expected]
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;expected&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;list[str]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;matches&#x22;" type="&#x22;(self, expected, predicted) -> bool&#x22;">
  <PySourceCode>
    ```python
    def matches(self, expected: str, predicted: str) -> bool:
        if not expected or not predicted:
            return False
        if expected == predicted:
            return True
        cls_a = self._equivalence.get(expected)
        return bool(cls_a) and predicted in cls_a
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;expected&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;predicted&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;bool&#x22;" />
</PyFunction>

<PyFunction name="&#x22;found_in&#x22;" type="&#x22;(self, expected, returned) -> bool&#x22;">
  True if any accepted slug appears in `returned` (a list of slugs).

  <PySourceCode>
    ```python
    def found_in(self, expected: str, returned: list[str]) -> bool:
        """True if any accepted slug appears in ``returned`` (a list of slugs)."""
        if not expected or not returned:
            return False
        accepted = self._equivalence.get(expected, {expected})
        return any(s in accepted for s in returned)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;expected&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;returned&#x22;" type="&#x22;list[str]&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;bool&#x22;" />
</PyFunction>

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, primary_aliases=dict(), secondary_aliases=dict(), bidirectional=True) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;primary_aliases&#x22;" type="&#x22;dict[str, str]&#x22;" value="&#x22;dict()&#x22;" />

    <PyParameter name="&#x22;secondary_aliases&#x22;" type="&#x22;dict[str, list[str]]&#x22;" value="&#x22;dict()&#x22;" />

    <PyParameter name="&#x22;bidirectional&#x22;" type="&#x22;bool&#x22;" value="&#x22;True&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
