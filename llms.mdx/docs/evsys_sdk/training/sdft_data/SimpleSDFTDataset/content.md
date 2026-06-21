# SimpleSDFTDataset (/docs/evsys_sdk/training/sdft_data/SimpleSDFTDataset)



Stock :class:`SDFTDataset` over :class:`~evsys_sdk.data_types.PromptExample`
rows: the question lives in `inputs['question']` and the gold answer in
`expected`. Wraps modulo dataset length so the loop can exceed one epoch
(no `_RepeatingSDFTProvider` hack needed).

## Attributes [#attributes]

<PyAttribute name="&#x22;rows&#x22;" type="&#x22;list[PromptExample]&#x22;" value="null" />

<PyAttribute name="&#x22;batch_size&#x22;" type="&#x22;int&#x22;" value="null" />

## Functions [#functions]

<PyFunction name="&#x22;__post_init__&#x22;" type="&#x22;(self) -> None&#x22;">
  <PySourceCode>
    ```python
    def __post_init__(self) -> None:
        if not self.rows:
            raise ValueError("SimpleSDFTDataset: rows is empty")
        if self.batch_size <= 0:
            raise ValueError(f"batch_size must be > 0 (got {self.batch_size})")
        missing = [
            i for i, r in enumerate(self.rows[:5])
            if not r.inputs.get("question") or r.expected is None
        ]
        if missing:
            raise ValueError(
                f"SimpleSDFTDataset: rows need inputs['question'] + expected "
                f"(indices {missing} of first 5)"
            )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;__len__&#x22;" type="&#x22;(self) -> int&#x22;">
  <PySourceCode>
    ```python
    def __len__(self) -> int:
        return max(1, len(self.rows) // self.batch_size)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;int&#x22;" />
</PyFunction>

<PyFunction name="&#x22;get_batch&#x22;" type="&#x22;(self, step_idx) -> tuple[list[str], list[str]]&#x22;">
  <PySourceCode>
    ```python
    def get_batch(self, step_idx: int) -> tuple[list[str], list[str]]:
        n = len(self.rows)
        start = (step_idx * self.batch_size) % n
        end = start + self.batch_size
        if end <= n:
            slice_ = self.rows[start:end]
        else:
            slice_ = self.rows[start:] + self.rows[: end - n]
        return (
            [r.inputs["question"] for r in slice_],
            [str(r.expected) for r in slice_],
        )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;step_idx&#x22;" type="&#x22;int&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;tuple[list[str], list[str]]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, rows, batch_size) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;rows&#x22;" type="&#x22;list[PromptExample]&#x22;" value="null" />

    <PyParameter name="&#x22;batch_size&#x22;" type="&#x22;int&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
