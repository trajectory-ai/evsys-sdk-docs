# Registry (/docs/evsys_sdk/registry/Registry)



Generic name->class registry.

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, kind) -> None&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, kind: str) -> None:
        self._kind = kind
        self._items: dict[str, type] = {}
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;kind&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>

<PyFunction name="&#x22;register&#x22;" type="&#x22;(self, name=None) -> Callable[[type[T]], type[T]]&#x22;">
  <PySourceCode>
    ```python
    def register(self, name: str | None = None) -> Callable[[type[T]], type[T]]:
        def decorator(cls: type[T]) -> type[T]:
            key = name or getattr(cls, "name", None)
            if not key:
                raise ValueError(
                    f"{self._kind} class {cls.__name__} has no `name` and "
                    f"register_{self._kind} was called without a name."
                )
            if key in self._items and self._items[key] is not cls:
                raise ValueError(
                    f"{self._kind} '{key}' already registered "
                    f"(existing={self._items[key].__module__}.{self._items[key].__name__}, "
                    f"new={cls.__module__}.{cls.__name__})"
                )
            # Best-effort: ensure the class declares its name attribute
            try:
                setattr(cls, "name", key)
            except (TypeError, AttributeError):
                pass
            self._items[key] = cls
            return cls

        return decorator
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str | None&#x22;" value="&#x22;None&#x22;" />
  </div>

  <PyFunctionReturn type="&#x22;typing.Callable[[type[evsys_sdk.registry.T]], type[evsys_sdk.registry.T]]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;get&#x22;" type="&#x22;(self, name) -> type&#x22;">
  <PySourceCode>
    ```python
    def get(self, name: str) -> type:
        if name not in self._items:
            available = ", ".join(sorted(self._items)) or "(none)"
            raise KeyError(
                f"No {self._kind} registered under '{name}'. Available: {available}"
            )
        return self._items[name]
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;type&#x22;" />
</PyFunction>

<PyFunction name="&#x22;list&#x22;" type="&#x22;(self) -> list[str]&#x22;">
  <PySourceCode>
    ```python
    def list(self) -> list[str]:
        return sorted(self._items)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.registry.Registry.list[str]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;has&#x22;" type="&#x22;(self, name) -> bool&#x22;">
  <PySourceCode>
    ```python
    def has(self, name: str) -> bool:
        return name in self._items
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;bool&#x22;" />
</PyFunction>

<PyFunction name="&#x22;items&#x22;" type="&#x22;(self) -> list[tuple[str, type]]&#x22;">
  <PySourceCode>
    ```python
    def items(self) -> list[tuple[str, type]]:
        return sorted(self._items.items())
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;evsys_sdk.registry.Registry.list[tuple[str, type]]&#x22;" />
</PyFunction>

<PyFunction name="&#x22;unregister&#x22;" type="&#x22;(self, name) -> None&#x22;">
  Remove an entry. Mostly for tests.

  <PySourceCode>
    ```python
    def unregister(self, name: str) -> None:
        """Remove an entry. Mostly for tests."""
        self._items.pop(name, None)
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;name&#x22;" type="&#x22;str&#x22;" value="null" />
  </div>

  <PyFunctionReturn type="&#x22;None&#x22;" />
</PyFunction>
