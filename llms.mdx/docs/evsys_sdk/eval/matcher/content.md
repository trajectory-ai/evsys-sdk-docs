# matcher (/docs/evsys_sdk/eval/matcher)



Alias-aware slug matching for tool-detection evals.

Wraps verified\_aliases.json and an optional secondary-aliases file so a
predicted slug counts as correct if it equals the expected slug OR any
of the expected slug's verified aliases.

Bidirectional by default: an alias map `\{old: new\}` is treated as a
symmetric equivalence. This matters when the eval set has been renamed
to canonical/new slugs but the model under test was trained on the
old slugs (or vice versa) — both names should count as the same answer.

<Tabs items="[&#x22;Class&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;AliasMatcher&#x22;" href="&#x22;/docs/evsys_sdk/eval/matcher/AliasMatcher&#x22;" />
    </Cards>
  </Tab>
</Tabs>
