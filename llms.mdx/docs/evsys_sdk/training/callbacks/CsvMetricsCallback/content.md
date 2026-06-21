# CsvMetricsCallback (/docs/evsys_sdk/training/callbacks/CsvMetricsCallback)



Mirror the loop's per-step metric writes into a CSV alongside
`metrics.jsonl`. Friendly for pandas / spreadsheet inspection.

## Attributes [#attributes]

<PyAttribute name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="&#x22;'csv_metrics'&#x22;" />

<PyAttribute name="&#x22;Config&#x22;" type="&#x22;type&#x22;" value="&#x22;CsvMetricsConfig&#x22;" />

<PyAttribute name="&#x22;out_path&#x22;" type="&#x22;Path&#x22;" value="null" />

<PyAttribute name="&#x22;delimiter&#x22;" type="&#x22;str&#x22;" value="&#x22;','&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;on_train_start&#x22;" type="&#x22;(self, state)&#x22;">
  <PySourceCode>
    ```python
    def on_train_start(self, state):
        Path(self.out_path).parent.mkdir(parents=True, exist_ok=True)
        self._fh = open(self.out_path, "w", newline="", encoding="utf-8")
        self._writer = csv.writer(self._fh, delimiter=self.delimiter)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;state&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="null" />
</PyFunction>

<PyFunction name="&#x22;on_step_end&#x22;" type="&#x22;(self, state, step_idx, batch, metrics)&#x22;">
  <PySourceCode>
    ```python
    def on_step_end(self, state, step_idx, batch, metrics):
        if self._writer is None:
            return
        if not self._header_written:
            self._keys = sorted(metrics.keys())
            self._writer.writerow(["step", *self._keys])
            self._header_written = True
        # When new keys appear later, append columns at the end (no rewrite).
        for k in metrics:
            if k not in self._keys:
                self._keys.append(k)
        self._writer.writerow(
            [step_idx, *[metrics.get(k, "") for k in self._keys]],
        )
        self._fh.flush()
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;state&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;step_idx&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;batch&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;metrics&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="null" />
</PyFunction>

<PyFunction name="&#x22;on_train_end&#x22;" type="&#x22;(self, state, artifacts)&#x22;">
  <PySourceCode>
    ```python
    def on_train_end(self, state, artifacts):
        if self._fh is not None:
            try:
                self._fh.close()
            except Exception:
                logger.exception("CsvMetricsCallback: close failed")
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;state&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;artifacts&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="null" />
</PyFunction>

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, out_path, delimiter=',') -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;out_path&#x22;" type="&#x22;Path&#x22;" value="null" />

    <PyParameter name="&#x22;delimiter&#x22;" type="&#x22;str&#x22;" value="&#x22;','&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
