# ColorFormatter (/docs/evsys_sdk/logger/ColorFormatter)



Wrap formatted records in ANSI color based on level (TTY only).

## Attributes [#attributes]

<PyAttribute name="&#x22;COLORS&#x22;" type="null" value="&#x22;{logging.DEBUG: GRAY, logging.INFO: GRAY, logging.WARNING: YELLOW, logging.ERROR: RED, logging.CRITICAL: RED}&#x22;" />

<PyAttribute name="&#x22;use_color&#x22;" type="null" value="&#x22;use_color and sys.stdout.isatty()&#x22;" />

## Functions [#functions]

<PyFunction name="&#x22;__init__&#x22;" type="&#x22;(self, fmt=None, datefmt=None, use_color=True)&#x22;">
  <PySourceCode>
    ```python
    def __init__(self, fmt=None, datefmt=None, use_color=True):
        super().__init__(fmt=fmt, datefmt=datefmt)
        self.use_color = use_color and sys.stdout.isatty()
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;fmt&#x22;" type="null" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;datefmt&#x22;" type="null" value="&#x22;None&#x22;" />

    <PyParameter name="&#x22;use_color&#x22;" type="null" value="&#x22;True&#x22;" />
  </div>

  <PyFunctionReturn type="null" />
</PyFunction>

<PyFunction name="&#x22;format&#x22;" type="&#x22;(self, record)&#x22;">
  <PySourceCode>
    ```python
    def format(self, record):
        message = super().format(record)
        if self.use_color:
            color = self.COLORS.get(record.levelno, "")
            if color:
                message = f"{color}{message}{RESET}"
        return message
    ```
  </PySourceCode>

  <div>
    <PyParameter name="&#x22;self&#x22;" type="null" value="null" />

    <PyParameter name="&#x22;record&#x22;" type="null" value="null" />
  </div>

  <PyFunctionReturn type="null" />
</PyFunction>
