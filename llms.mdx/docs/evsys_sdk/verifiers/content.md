# verifiers (/docs/evsys_sdk/verifiers)



Built-in verifiers.

Two layers:

* `@register_verifier` *classes* (runtime `Verifier` Protocol).
* in-process verifier *functions* in `fns` — the SDK-local registry that
  `InProcessVerifier(fn_name=…)` resolves against (D10: SDK is the single
  source of truth; the remote stores only `fn_name` + `expected`/`params`).

<Tabs items="[&#x22;Modules&#x22;]">
  <Tab value="&#x22;Modules&#x22;">
    <Cards>
      <Card href="&#x22;/docs/evsys_sdk/verifiers/format_only&#x22;" title="&#x22;format_only&#x22;" />

      <Card href="&#x22;/docs/evsys_sdk/verifiers/fns&#x22;" title="&#x22;fns&#x22;" />
    </Cards>
  </Tab>
</Tabs>
