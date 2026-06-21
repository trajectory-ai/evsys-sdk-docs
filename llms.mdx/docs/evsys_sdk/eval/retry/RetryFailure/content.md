# RetryFailure (/docs/evsys_sdk/eval/retry/RetryFailure)



One exhausted-retry event.

## Attributes [#attributes]

<PyAttribute name="&#x22;context&#x22;" type="&#x22;str&#x22;" value="null">
  Free-form label for the call site (e.g. 'eval:task42:sample0').
</PyAttribute>

<PyAttribute name="&#x22;exception_type&#x22;" type="&#x22;str&#x22;" value="null" />

<PyAttribute name="&#x22;exception_message&#x22;" type="&#x22;str&#x22;" value="null" />

<PyAttribute name="&#x22;attempts&#x22;" type="&#x22;int&#x22;" value="null" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, context, exception_type, exception_message, attempts) -> None&#x22;">
  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;context&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;exception_type&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;exception_message&#x22;" type="&#x22;str&#x22;" value="null" />

    <PyParameter name="&#x22;attempts&#x22;" type="&#x22;int&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
