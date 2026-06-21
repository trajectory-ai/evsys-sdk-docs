# SDFTDataset (/docs/evsys_sdk/training/sdft_data/SDFTDataset)



The data interface the SDFT algorithm consumes per step.

Per the SDFT paper, each step needs `batch_size` `(question, golden_answer)`
pairs — the student rolls out on the question, the teacher scores
teacher-forced through the question+golden\_answer demo.

## Functions [#functions]

<PyFunction name="&#x22;__len__&#x22;" type="&#x22;(self) -> int&#x22;">
  <PySourceCode>
    ```python
    def __len__(self) -> int: ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;int&#x22;" />
</PyFunction>

<PyFunction name="&#x22;get_batch&#x22;" type="&#x22;(self, step_idx) -> tuple[list[str], list[str]]&#x22;">
  Return `(questions, golden_answers)` of length `batch_size`.

  <PySourceCode>
    ```python
    def get_batch(self, step_idx: int) -> tuple[list[str], list[str]]:
        """Return ``(questions, golden_answers)`` of length ``batch_size``."""
        ...
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;step_idx&#x22;" type="&#x22;int&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;tuple[list[str], list[str]]&#x22;" />
</PyFunction>
