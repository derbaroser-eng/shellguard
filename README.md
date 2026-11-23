# shellguard

Run untrusted text as **arguments**, never as shell syntax.

## The bug

```python
full = f"cd {dir} && run --model {model} {prompt}"
subprocess.run(full, shell=True)
```

A prompt containing `(` makes bash abort with `syntax error near unexpected
token '('`. A prompt containing backticks or dollar-parens executes attacker-chosen
commands. Both failures were observed in production.

## The rule

Never build a shell string from untrusted text. Build an argv list; execve
semantics keep each token a token.

## Usage

```python
import shellguard as sg

sg.run(["echo", "(17*23)"])          # -> "391\n"
sg.run(["echo", "`echo INJECTED`"])  # -> literal text
sg.run(["echo", "$(echo INJECTED)"]) # -> literal text
sg.run(["false"])                    # -> Result(returncode=1, ok=False, ...)
```

## What you receive

- `src/shellguard/__init__.py` - the library
- `tests/test_shellguard.py` - 26 real attack cases (real subprocess, real exit codes)
- `SPEC.md` - inputs, outputs, exit codes, non-goals
- a short integration guide

Zero dependencies. Python 3.8+.

## Tests

```
python tests/test_shellguard.py
```

26 real attack cases: parentheses, backticks, dollar-parens, semicolons, pipes,
ampersands, newline, quotes, globs, tilde, env expansion, redirection, unicode,
empty args, binary bytes, timeouts, cwd, env, exit codes, and stdout/stderr
separation.

## License

MIT.

## Writing

- [My shell-injection scanner flagged 3 of my own files. All 3 were wrong.](https://dev.to/aurum_revenue/my-shell-injection-scanner-flagged-3-of-my-own-files-all-3-were-wrong-13ho) - running `sg audit` across 90 Python files: 3 real findings, 3 false positives, and the exact rule that caused them.
- [I switched to an argv list. The shell still ran my input.](https://dev.to/aurum_revenue/i-switched-to-an-argv-list-the-shell-still-ran-my-input-2cla) - SG005: an argv list whose first element is a shell and whose command comes from untrusted text still hands that text to a shell parser.
