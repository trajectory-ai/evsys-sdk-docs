# RetryReport (/docs/evsys_sdk/eval/retry/RetryReport)



Collects retry-exhausted failures during an eval pass.

## Attributes [#attributes]

<PyAttribute name="&#x22;failures&#x22;" type="&#x22;list[RetryFailure]&#x22;" value="&#x22;field(default_factory=list)&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;record&#x22;" type="&#x22;(self, context, exc, attempts) -> None&#x22;">
  <PySourceCode>
    ```python
    def record(self, context: str, exc: BaseException, attempts: int) -> None:
        self.failures.append(
            RetryFailure(
                context=context,
                exception_type=type(exc).__name__,
                exception_message=str(exc),
                attempts=attempts,
            )
        )
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;context&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;exc&#x22;" type="&#x22;BaseException&#x22;" value="null" />

    <PyParameter name="&#x22;attempts&#x22;" type="&#x22;int&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;as_dict&#x22;" type="&#x22;(self) -> dict[str, Any]&#x22;">
  <PySourceCode>
    ```python
    def as_dict(self) -> dict[str, Any]:
        return {
            "total_failures": len(self.failures),
            "by_exception_type": self._by_type(),
            "failures": [
                {
                    "context": f.context,
                    "exception_type": f.exception_type,
                    "exception_message": f.exception_message,
                    "attempts": f.attempts,
                }
                for f in self.failures
            ],
        }
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;dict[str, typing.Any]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;_by_type&#x22;" type="&#x22;(self) -> dict[str, int]&#x22;">
  <PySourceCode>
    ```python
    def _by_type(self) -> dict[str, int]:
        counts: dict[str, int] = {}
        for f in self.failures:
            counts[f.exception_type] = counts.get(f.exception_type, 0) + 1
        return counts
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;dict[str, int]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, failures=list()) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;failures&#x22;" type="&#x22;list[RetryFailure]&#x22;" value="&#x22;list()&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
