# retry (/docs/evsys_sdk/eval/retry)



Retry decorator + report for connection-style transient errors.

Wraps a callable; retries on a configurable set of exceptions with exponential
backoff. Exhausted failures are pushed onto a RetryReport so the eval harness
can surface them in the final report rather than aborting.

<PyAttribute name="&#x22;T&#x22;" type="null" value="&#x22;TypeVar('T')&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;RetryFailure&#x22;" href="&#x22;/docs/evsys_sdk/eval/retry/RetryFailure&#x22;" />

      <Card title="&#x22;RetryReport&#x22;" href="&#x22;/docs/evsys_sdk/eval/retry/RetryReport&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;_default_retriable_exceptions&#x22;" type="&#x22;() -> tuple[type[BaseException], ...]&#x22;">
      <PySourceCode>
        ```python
        def _default_retriable_exceptions() -> tuple[type[BaseException], ...]:
            types: list[type[BaseException]] = [ConnectionError, TimeoutError]
            try:
                import httpx

                types += [
                    httpx.ConnectError,
                    httpx.ReadTimeout,
                    httpx.RemoteProtocolError,
                    httpx.LocalProtocolError,
                ]
            except ImportError:
                pass
            return tuple(types)
        ```
      </PySourceCode>

      <PyFunctionReturn type="&#x22;tuple[type[BaseException], ...]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;call_with_retry&#x22;" type="&#x22;(fn, *args, *, max_attempts=5, backoff_base=1.0, backoff_cap=16.0, retriable=None, report=None, context='', **kwargs) -> T | None&#x22;">
      Call `fn(*args, **kwargs)` retrying on transient errors.

      On exhaustion: records to `report` (if supplied) and returns `None`.
      Non-transient exceptions propagate immediately — eval bugs shouldn't be
      silently retried.

      <PySourceCode>
        ```python
        def call_with_retry(
            fn: Callable[..., T],
            *args: Any,
            max_attempts: int = 5,
            backoff_base: float = 1.0,
            backoff_cap: float = 16.0,
            retriable: tuple[type[BaseException], ...] | None = None,
            report: RetryReport | None = None,
            context: str = "",
            **kwargs: Any,
        ) -> T | None:
            """Call ``fn(*args, **kwargs)`` retrying on transient errors.

            On exhaustion: records to ``report`` (if supplied) and returns ``None``.
            Non-transient exceptions propagate immediately — eval bugs shouldn't be
            silently retried.
            """
            if retriable is None:
                retriable = _default_retriable_exceptions()

            last_exc: BaseException | None = None
            for attempt in range(1, max_attempts + 1):
                try:
                    return fn(*args, **kwargs)
                except retriable as exc:
                    last_exc = exc
                    if attempt == max_attempts:
                        break
                    delay = min(backoff_cap, backoff_base * (2 ** (attempt - 1)))
                    time.sleep(delay)
            if report is not None and last_exc is not None:
                report.record(context, last_exc, max_attempts)
            return None
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;fn&#x22;" type="&#x22;Callable[..., T]&#x22;" value="null" />

        <PyParameter name="&#x22;args&#x22;" type="&#x22;Any&#x22;" value="&#x22;()&#x22;" />

        <PyParameter name="&#x22;max_attempts&#x22;" type="&#x22;int&#x22;" value="&#x22;5&#x22;" />

        <PyParameter name="&#x22;backoff_base&#x22;" type="&#x22;float&#x22;" value="&#x22;1.0&#x22;" />

        <PyParameter name="&#x22;backoff_cap&#x22;" type="&#x22;float&#x22;" value="&#x22;16.0&#x22;" />

        <PyParameter name="&#x22;retriable&#x22;" type="&#x22;tuple[type[BaseException], ...] | None&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;report&#x22;" type="&#x22;RetryReport | None&#x22;" value="&#x22;None&#x22;" />

        <PyParameter name="&#x22;context&#x22;" type="&#x22;str&#x22;" value="&#x22;''&#x22;" />

        <PyParameter name="&#x22;kwargs&#x22;" type="&#x22;Any&#x22;" value="&#x22;{}&#x22;" />
      </div>

      <PyFunctionReturn type="&#x22;evsys_sdk.eval.retry.T | None&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
