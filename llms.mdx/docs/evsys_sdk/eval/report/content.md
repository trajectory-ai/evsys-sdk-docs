# report (/docs/evsys_sdk/eval/report)



Pass\@1/pass\@3/pass^3 aggregation + retry-failure section.

Works on any per-row, per-query result shape: the caller supplies a function
that maps one query-result to a bool `found`, so the same scorer serves
model evals and any custom eval harness.

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;EvalSummary&#x22;" href="&#x22;/docs/evsys_sdk/eval/report/EvalSummary&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;model_query_found&#x22;" type="&#x22;(qresult, expected_slug, matcher) -> bool&#x22;">
      <PySourceCode>
        ```python
        def model_query_found(qresult: dict[str, Any], expected_slug: str, matcher: AliasMatcher) -> bool:
            if qresult.get("error"):
                return False
            return matcher.matches(expected_slug, qresult.get("predicted", ""))
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;qresult&#x22;" type="&#x22;dict[str, Any]&#x22;" value="null" />

        <PyParameter name="&#x22;expected_slug&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;matcher&#x22;" type="&#x22;AliasMatcher&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;bool&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;score_rows&#x22;" type="&#x22;(rows, *, matcher, query_found_fn, retry_report=None) -> EvalSummary&#x22;">
      <PySourceCode>
        ```python
        def score_rows(
            rows: list[dict[str, Any]],
            *,
            matcher: AliasMatcher,
            query_found_fn: Callable[[dict[str, Any], str, AliasMatcher], bool],
            retry_report: RetryReport | None = None,
        ) -> EvalSummary:
            if not rows:
                return EvalSummary(0.0, 0.0, 0.0, 0, 0)

            per_toolkit_buckets: dict[str, dict[str, list[float]]] = {}
            sums = {"pass_at_1": 0.0, "pass_at_3": 0.0, "pass_pow_3": 0.0}
            n_queries = 0

            for row in rows:
                expected = row.get("tool_slug", "")
                tk = row.get("toolkit", "")
                qs = row.get("queries", [])
                flags = [query_found_fn(q, expected, matcher) for q in qs]
                n_queries += len(qs)

                if not flags:
                    continue
                p1 = 1.0 if flags[0] else 0.0
                p3 = 1.0 if any(flags) else 0.0
                pp3 = 1.0 if all(flags) else 0.0

                sums["pass_at_1"] += p1
                sums["pass_at_3"] += p3
                sums["pass_pow_3"] += pp3

                bucket = per_toolkit_buckets.setdefault(
                    tk, {"pass_at_1": [], "pass_at_3": [], "pass_pow_3": []}
                )
                bucket["pass_at_1"].append(p1)
                bucket["pass_at_3"].append(p3)
                bucket["pass_pow_3"].append(pp3)

            n = len(rows)
            per_toolkit: dict[str, dict[str, float]] = {}
            for tk, b in per_toolkit_buckets.items():
                cnt = len(b["pass_at_1"])
                per_toolkit[tk] = {
                    "count": cnt,
                    "pass_at_1": round(sum(b["pass_at_1"]) / cnt, 4),
                    "pass_at_3": round(sum(b["pass_at_3"]) / cnt, 4),
                    "pass_pow_3": round(sum(b["pass_pow_3"]) / cnt, 4),
                }

            summary = EvalSummary(
                pass_at_1=sums["pass_at_1"] / n,
                pass_at_3=sums["pass_at_3"] / n,
                pass_pow_3=sums["pass_pow_3"] / n,
                n_rows=n,
                n_queries=n_queries,
                per_toolkit=per_toolkit,
                retry_report=retry_report.as_dict() if retry_report else {"total_failures": 0, "failures": []},
            )
            return summary
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;rows&#x22;" type="&#x22;list[dict[str, Any]]&#x22;" value="null" />

        <PyParameter name="&#x22;matcher&#x22;" type="&#x22;AliasMatcher&#x22;" value="null" />

        <PyParameter name="&#x22;query_found_fn&#x22;" type="&#x22;Callable[[dict[str, Any], str, AliasMatcher], bool]&#x22;" value="null" />

        <PyParameter name="&#x22;retry_report&#x22;" type="&#x22;RetryReport | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="&#x22;evsys_sdk.eval.report.EvalSummary&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;format_summary_markdown&#x22;" type="&#x22;(summary, *, title='Eval Summary') -> str&#x22;">
      <PySourceCode>
        ```python
        def format_summary_markdown(summary: EvalSummary, *, title: str = "Eval Summary") -> str:
            o = summary.as_dict()
            lines = [
                f"# {title}",
                "",
                f"- Rows: **{summary.n_rows}**, queries: **{summary.n_queries}**",
                f"- pass@1: **{o['overall']['pass_at_1']:.1%}**",
                f"- pass@3: **{o['overall']['pass_at_3']:.1%}**",
                f"- pass^3: **{o['overall']['pass_pow_3']:.1%}**",
            ]
            rr = o.get("retry_report", {})
            if rr.get("total_failures"):
                lines += [
                    "",
                    f"## Retry-exhausted failures: {rr['total_failures']}",
                    "",
                    "| Exception | Count |",
                    "|---|---|",
                ]
                for k, v in (rr.get("by_exception_type") or {}).items():
                    lines.append(f"| `{k}` | {v} |")
            if summary.per_toolkit:
                lines += ["", "## Per toolkit", "", "| Toolkit | N | pass@1 | pass@3 | pass^3 |", "|---|---|---|---|---|"]
                for tk in sorted(summary.per_toolkit):
                    m = summary.per_toolkit[tk]
                    lines.append(
                        f"| {tk} | {m['count']} | {m['pass_at_1']:.1%} | {m['pass_at_3']:.1%} | {m['pass_pow_3']:.1%} |"
                    )
            if summary.notes:
                lines += ["", "## Notes", ""] + [f"- {n}" for n in summary.notes]
            return "\n".join(lines) + "\n"
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;summary&#x22;" type="&#x22;EvalSummary&#x22;" value="null" />

        <PyParameter name="&#x22;title&#x22;" type="&#x22;str&#x22;" value="&#x22;'Eval Summary'&#x22;" />
      </div>

      <PyFunctionReturn type="&#x22;str&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
