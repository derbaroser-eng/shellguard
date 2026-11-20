# shellguard

Run untrusted text as **arguments**, never as shell syntax.

`pip install shellguard` — zero dependencies, Python 3.8+, MIT licensed.

```python
import shellguard as sg

sg.run(["echo", "(17*23)"])          # '(17*23)\n'
sg.run(["echo", "`echo INJECTED`"])  # literal text; nothing executes
sg.run(["echo", "$(rm -rf /)"])      # literal text
```

`sg.run()` refuses a bare string command and refuses `shell=True`, because both
would hand untrusted text to a shell parser.

26 tests cover parentheses, quotes, backticks, `$( )`, `;`, `|`, `&&`, newline,
globs, tilde, `$VAR`, redirection, unicode, empty arguments, binary bytes,
timeouts, working directory, environment, exit codes and stdout/stderr
separation.

MIT License. See LICENSE.
