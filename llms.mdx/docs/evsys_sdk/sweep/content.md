# sweep (/docs/evsys_sdk/sweep)



Sweep — declarative axes over a single `RunConfig`.

A `Sweep` is the OOP way to describe what YAML's `matrix:` block describes:
one `base_run` template + per-axis lists of values, expanded by cartesian
product into N concrete `RunConfig` rows.

This is what an experiment script reaches for instead of writing a per-axis
`for` loop. Sweep + Experiment together let `run.py` stay declarative:

sweep = Sweep(base\_run, \{"algorithm.params.lora\_rank": \[1, 4, 16]})
for run in sweep.expand():
...

Or, more commonly, from a YAML matrix block already parsed into
`MatrixSpec` — round-trip with `Sweep.from_matrix` / `.to_matrix`.

The expansion is the single source of truth for both the YAML `matrix:`
loader and programmatic builds (the legacy `_expand_matrix` delegates here).

<PyAttribute name="&#x22;__all__&#x22;" type="null" value="&#x22;['Sweep', 'expand_runs']&#x22;" />

<Tabs items="[&#x22;Class&#x22;,&#x22;Functions&#x22;]">
  <Tab value="&#x22;Class&#x22;">
    <Cards>
      <Card title="&#x22;Sweep&#x22;" href="&#x22;/docs/evsys_sdk/sweep/Sweep&#x22;" />
    </Cards>
  </Tab>

  <Tab value="&#x22;Functions&#x22;">
    <PyFunction name="&#x22;expand_runs&#x22;" type="&#x22;(base_run, axes, name_template=None) -> list[RunConfig]&#x22;">
      Cartesian product of `axes` over `base_run`.

      Single source of truth for matrix expansion — both `Sweep.expand` and
      the YAML loader's `_expand_matrix` route through here. Empty `axes`
      returns `[base_run]` unchanged.

      <PySourceCode>
        ```python
        def expand_runs(
            base_run: RunConfig,
            axes: dict[str, list[Any]],
            name_template: str | None = None,
        ) -> list[RunConfig]:
            """Cartesian product of ``axes`` over ``base_run``.

            Single source of truth for matrix expansion — both ``Sweep.expand`` and
            the YAML loader's ``_expand_matrix`` route through here. Empty ``axes``
            returns ``[base_run]`` unchanged.
            """
            if not axes:
                return [base_run.model_copy(deep=True)]

            axis_names = list(axes.keys())
            axis_values = [axes[a] for a in axis_names]

            runs: list[RunConfig] = []
            for combo in product(*axis_values):
                new_dict = base_run.model_dump()
                binding: dict[str, str] = {"base": base_run.name}
                for axis, value in zip(axis_names, combo):
                    _set_dotted(new_dict, axis, value)
                    binding[axis] = _format_value_for_name(value)

                new_run = RunConfig.model_validate(new_dict)
                new_run.name = _render_name(name_template, binding, axis_names, combo, base_run.name)
                runs.append(new_run)
            return runs
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;base_run&#x22;" type="&#x22;RunConfig&#x22;" value="null" />

        <PyParameter name="&#x22;axes&#x22;" type="&#x22;dict[str, list[Any]]&#x22;" value="null" />

        <PyParameter name="&#x22;name_template&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
      </div>

      <PyFunctionReturn type="&#x22;list[evsys_sdk.config.RunConfig]&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_render_name&#x22;" type="&#x22;(template, binding, axis_names, combo, base_name) -> str&#x22;">
      <PySourceCode>
        ```python
        def _render_name(
            template: str | None,
            binding: dict[str, str],
            axis_names: list[str],
            combo: tuple[Any, ...],
            base_name: str,
        ) -> str:
            if template is not None:
                return _render_name_template(template, binding)
            suffix = "_".join(
                f"{axis.split('.')[-1]}{_format_value_for_name(value)}"
                for axis, value in zip(axis_names, combo)
            )
            return f"{base_name}__{suffix}" if suffix else base_name
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;template&#x22;" type="&#x22;str | None&#x22;" value="null" />

        <PyParameter name="&#x22;binding&#x22;" type="&#x22;dict[str, str]&#x22;" value="null" />

        <PyParameter name="&#x22;axis_names&#x22;" type="&#x22;list[str]&#x22;" value="null" />

        <PyParameter name="&#x22;combo&#x22;" type="&#x22;tuple[Any, ...]&#x22;" value="null" />

        <PyParameter name="&#x22;base_name&#x22;" type="&#x22;str&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;str&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_render_name_template&#x22;" type="&#x22;(template, binding) -> str&#x22;">
      Substitute `\{dotted.key\}` placeholders without going through str.format.

      `str.format` treats dots as attribute access; we just want literal
      dotted keys.

      <PySourceCode>
        ```python
        def _render_name_template(template: str, binding: dict[str, str]) -> str:
            """Substitute ``{dotted.key}`` placeholders without going through str.format.

            ``str.format`` treats dots as attribute access; we just want literal
            dotted keys.
            """

            def repl(m: re.Match[str]) -> str:
                key = m.group(1)
                if key not in binding:
                    raise ValueError(f"name_template references unknown key '{key}'")
                return binding[key]

            return _TEMPLATE_RE.sub(repl, template)
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;template&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;binding&#x22;" type="&#x22;dict[str, str]&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;str&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_set_dotted&#x22;" type="&#x22;(obj, path, value) -> None&#x22;">
      Walk a dotted path into a nested-dict tree and set the leaf value.

      Always called on a `RunConfig.model_dump()` result, so every node is a
      dict; intermediate keys missing on params-style dicts are auto-created
      (with `setdefault`), and a typo in a strict path is caught later by
      `RunConfig.model_validate`.

      <PySourceCode>
        ```python
        def _set_dotted(obj: dict, path: str, value: Any) -> None:
            """Walk a dotted path into a nested-dict tree and set the leaf value.

            Always called on a ``RunConfig.model_dump()`` result, so every node is a
            dict; intermediate keys missing on params-style dicts are auto-created
            (with ``setdefault``), and a typo in a strict path is caught later by
            ``RunConfig.model_validate``.
            """
            parts = path.split(".")
            for p in parts[:-1]:
                obj = obj.setdefault(p, {})
            obj[parts[-1]] = value
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;obj&#x22;" type="&#x22;dict&#x22;" value="null" />

        <PyParameter name="&#x22;path&#x22;" type="&#x22;str&#x22;" value="null" />

        <PyParameter name="&#x22;value&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;None&#x22;" />
    </PyFunction>

    <PyFunction name="&#x22;_format_value_for_name&#x22;" type="&#x22;(v) -> str&#x22;">
      <PySourceCode>
        ```python
        def _format_value_for_name(v: Any) -> str:
            if isinstance(v, float):
                return f"{v:g}".replace("+", "").replace(".", "p")
            return str(v).replace("/", "_")
        ```
      </PySourceCode>

      <div>
        <PyParameter name="&#x22;v&#x22;" type="&#x22;Any&#x22;" value="null" />
      </div>

      <PyFunctionReturn type="&#x22;str&#x22;" />
    </PyFunction>
  </Tab>
</Tabs>
