# fns (/docs/evsys_sdk/verifiers/fns)



In-process verifier functions — the SDK-local registry (D10).

`InProcessVerifier(fn_name="tool_calls_match", expected=..., params=...)` stores
only a *reference*; the actual function lives here, in the SDK. This is the
single source of truth for in-process verifier logic — the remote (Supabase)
never stores verifier code, only the `fn_name` + per-task `expected`/`params`.

Ported from the backend `api/experiments/verifiers.py` `_REGISTRY` so there is
exactly ONE registry. Resolve with `get(fn_name)`; extend with
`register(fn_name, fn)` or the `@register_fn("name")` decorator.

Reproducibility note: because a task references a verifier by name, the named
function resolves against *this SDK version* — record the SDK version on the
experiment/run (see EXPERIMENT\_AGENT\_DESIGN.md, D10 / Q-J).

<PyAttribute name="&#x22;VerifierFn&#x22;" type="null" value="&#x22;Callable[[str, Any, dict], float]&#x22;" />

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['VerifierFn', 'tool_calls_match', 'exact_match', 'contains', 'regex_match', 'get', 'register', 'register_fn', 'list_fns']&#x22;" />

<Tabs items="[&#x22;Functions&#x22;]">
  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;_normalize_json&#x22;" type="&#x22;(s) -> dict | None&#x22;">
      Best-effort: strip code fences, find first JSON object, parse.

      <PySourceCode>
        ````python
        def _normalize_json(s: str) -> dict | None:
            """Best-effort: strip code fences, find first JSON object, parse."""
            s = (s or "").strip()
            s = re.sub(r"^\```(?:json)?\s*", "", s)
            s = re.sub(r"\s*\```$", "", s)
            if s.startswith("{") and s.endswith("}"):
                try:
                    return json.loads(s)
                except Exception:
                    pass
            m = re.search(r"\{.*\}", s, re.DOTALL)
            if m:
                try:
                    return json.loads(m.group(0))
                except Exception:
                    return None
            return None
        ````
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;s&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;dict | None&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;tool_calls_match&#x22;" type="&#x22;(model_output, expected, params) -> float&#x22;">
      1.0 if the model's emitted tool call matches expected; else 0.0.

      Both must parse as JSON dicts; tool + action must match; optional
      `ref`/`text` must match if present in `expected`; `coordinate` must
      be within `params['coordinate_tolerance']` (default 25).

      <PySourceCode>
        ```python
        def tool_calls_match(model_output: str, expected: Any, params: dict) -> float:
            """1.0 if the model's emitted tool call matches expected; else 0.0.

            Both must parse as JSON dicts; tool + action must match; optional
            ``ref``/``text`` must match if present in ``expected``; ``coordinate`` must
            be within ``params['coordinate_tolerance']`` (default 25).
            """
            pred = _normalize_json(model_output)
            if not isinstance(pred, dict):
                return 0.0
            exp = expected if isinstance(expected, dict) else _normalize_json(expected or "")
            if not isinstance(exp, dict):
                return 0.0

            if pred.get("tool") != exp.get("tool"):
                return 0.0
            if pred.get("action") != exp.get("action"):
                return 0.0
            if "ref" in exp and pred.get("ref") != exp["ref"]:
                return 0.0
            if "text" in exp and pred.get("text") != exp["text"]:
                return 0.0
            if "coordinate" in exp:
                tol = int(params.get("coordinate_tolerance", 25))
                ec = exp["coordinate"]
                pc = pred.get("coordinate")
                if not (isinstance(pc, list) and len(pc) == 2):
                    return 0.0
                if abs(pc[0] - ec[0]) > tol or abs(pc[1] - ec[1]) > tol:
                    return 0.0
            return 1.0
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;model_output&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;expected&#x22;" type="&#x22;Any&#x22;" value="null" />

        <PyParameter name="&#x22;params&#x22;" type="&#x22;dict&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;float&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;exact_match&#x22;" type="&#x22;(model_output, expected, params) -> float&#x22;">
      Strict text equality (after whitespace strip + optional case-fold).

      <PySourceCode>
        ```python
        def exact_match(model_output: str, expected: Any, params: dict) -> float:
            """Strict text equality (after whitespace strip + optional case-fold)."""
            a = (model_output or "").strip()
            b = str(expected or "").strip()
            if params.get("ignore_case"):
                a, b = a.lower(), b.lower()
            return 1.0 if a == b else 0.0
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;model_output&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;expected&#x22;" type="&#x22;Any&#x22;" value="null" />

        <PyParameter name="&#x22;params&#x22;" type="&#x22;dict&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;float&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;contains&#x22;" type="&#x22;(model_output, expected, params) -> float&#x22;">
      1.0 if `expected` is a substring of model\_output.

      <PySourceCode>
        ```python
        def contains(model_output: str, expected: Any, params: dict) -> float:
            """1.0 if `expected` is a substring of model_output."""
            a = model_output or ""
            b = str(expected or "")
            if params.get("ignore_case"):
                a, b = a.lower(), b.lower()
            return 1.0 if b and b in a else 0.0
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;model_output&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;expected&#x22;" type="&#x22;Any&#x22;" value="null" />

        <PyParameter name="&#x22;params&#x22;" type="&#x22;dict&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;float&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;regex_match&#x22;" type="&#x22;(model_output, expected, params) -> float&#x22;">
      1.0 if regex `expected` matches model\_output.

      <PySourceCode>
        ```python
        def regex_match(model_output: str, expected: Any, params: dict) -> float:
            """1.0 if regex `expected` matches model_output."""
            pattern = str(expected or "")
            flags = re.IGNORECASE if params.get("ignore_case") else 0
            return 1.0 if pattern and re.search(pattern, model_output or "", flags) else 0.0
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;model_output&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;expected&#x22;" type="&#x22;Any&#x22;" value="null" />

        <PyParameter name="&#x22;params&#x22;" type="&#x22;dict&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;float&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;get&#x22;" type="&#x22;(fn_name) -> VerifierFn&#x22;">
      <PySourceCode>
        ```python
        def get(fn_name: str) -> VerifierFn:
            if fn_name not in _REGISTRY:
                raise ValueError(
                    f"Unknown verifier fn {fn_name!r}; registered: {sorted(_REGISTRY)}"
                )
            return _REGISTRY[fn_name]
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;fn_name&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;evsys_sdk.verifiers.fns.VerifierFn&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;register&#x22;" type="&#x22;(fn_name, fn) -> None&#x22;">
      <PySourceCode>
        ```python
        def register(fn_name: str, fn: VerifierFn) -> None:
            _REGISTRY[fn_name] = fn
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;fn_name&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;fn&#x22;" type="&#x22;VerifierFn&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;None&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;register_fn&#x22;" type="&#x22;(fn_name) -> Callable[[VerifierFn], VerifierFn]&#x22;">
      Decorator form of `register`.

      <PySourceCode>
        ```python
        def register_fn(fn_name: str) -> Callable[[VerifierFn], VerifierFn]:
            """Decorator form of ``register``."""

            def deco(fn: VerifierFn) -> VerifierFn:
                register(fn_name, fn)
                return fn

            return deco
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;fn_name&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;typing.Callable[[evsys_sdk.verifiers.fns.VerifierFn], evsys_sdk.verifiers.fns.VerifierFn]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;list_fns&#x22;" type="&#x22;() -> list[str]&#x22;">
      <PySourceCode>
        ```python
        def list_fns() -> list[str]:
            return sorted(_REGISTRY)
        ```
      </PySourceCode>

      <PyFunctionReturn type="&#x22;list[str]&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
