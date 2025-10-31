# Python `sys` Module Cheatsheet

The `sys` module provides access to system-specific parameters and functions that interact with the Python interpreter.

```python
import sys
```

## Core Attributes

| Attribute | Description | Example Usage |
|-----------|-------------|---------------|
| `sys.argv` | List of command-line arguments passed to script | `sys.argv[0]` (script name) |
| `sys.path` | List of directories Python searches for modules | `sys.path.append('/custom/path')` |
| `sys.version` | Python version string | `sys.version` |
| `sys.version_info` | Version info as named tuple | `sys.version_info.major` |
| `sys.platform` | Platform identifier | `'linux'`, `'win32'`, `'darwin'` |
| `sys.executable` | Path to Python interpreter | `/usr/bin/python3` |
| `sys.stdin` | Standard input file object | `sys.stdin.read()` |
| `sys.stdout` | Standard output file object | `sys.stdout.write('text')` |
| `sys.stderr` | Standard error file object | `sys.stderr.write('error')` |
| `sys.modules` | Dictionary of loaded modules | `sys.modules.keys()` |
| `sys.maxsize` | Maximum integer size | Platform-dependent integer |
| `sys.flags` | Command-line flags state | `sys.flags.debug` |
| `sys.prefix` | Site-specific directory prefix | Virtual environment path |

## Key Methods

| Method | Description | Returns |
|--------|-------------|---------|
| `sys.exit([status])` | Exit the program | NoReturn |
| `sys.getsizeof(object)` | Get object size in bytes | int |
| `sys.getrecursionlimit()` | Get max recursion depth | int |
| `sys.setrecursionlimit(limit)` | Set max recursion depth | None |
| `sys.getdefaultencoding()` | Get default string encoding | str |
| `sys.getfilesystemencoding()` | Get filesystem encoding | str |
| `sys.exc_info()` | Get current exception info | tuple |
| `sys.settrace(func)` | Set trace function for debugger | None |
| `sys.gettrace()` | Get current trace function | function/None |

## Practical Examples

### 1. Command-Line Arguments

```python
import sys

# script.py
if len(sys.argv) < 2:
    print(f"Usage: {sys.argv[0]} <filename>")
    sys.exit(1)

filename = sys.argv[1]
print(f"Processing: {filename}")
```

**Run:** `python script.py data.txt`

### 2. Platform-Specific Code

```python
import sys

if sys.platform == 'win32':
    print("Running on Windows")
    path_separator = '\\'
elif sys.platform == 'darwin':
    print("Running on macOS")
    path_separator = '/'
elif sys.platform.startswith('linux'):
    print("Running on Linux")
    path_separator = '/'
```

### 3. Redirecting Output

```python
import sys
from io import StringIO

# Capture stdout
old_stdout = sys.stdout
sys.stdout = StringIO()

print("This goes to buffer")
print("So does this")

# Get captured output
output = sys.stdout.getvalue()
sys.stdout = old_stdout

print(f"Captured: {output}")
```

### 4. Module Path Management

```python
import sys
import os

# Add parent directory to path
parent_dir = os.path.dirname(os.path.dirname(__file__))
sys.path.insert(0, parent_dir)

# Now can import from parent directory
from parent_module import something
```

### 5. Memory Usage Inspection

```python
import sys

data = [1, 2, 3, 4, 5]
print(f"List size: {sys.getsizeof(data)} bytes")

text = "Hello World"
print(f"String size: {sys.getsizeof(text)} bytes")

# Compare structures
dict_data = {'a': 1, 'b': 2}
print(f"Dict size: {sys.getsizeof(dict_data)} bytes")
```

### 6. Graceful Exit with Status Codes

```python
import sys

def process_file(filename):
    try:
        with open(filename, 'r') as f:
            return f.read()
    except FileNotFoundError:
        print(f"Error: File '{filename}' not found", file=sys.stderr)
        sys.exit(1)  # Exit with error code
    except PermissionError:
        print(f"Error: Permission denied", file=sys.stderr)
        sys.exit(2)

data = process_file('data.txt')
sys.exit(0)  # Exit successfully
```

### 7. Exception Information

```python
import sys

try:
    result = 10 / 0
except:
    exc_type, exc_value, exc_traceback = sys.exc_info()
    print(f"Exception type: {exc_type.__name__}")
    print(f"Exception message: {exc_value}")
    print(f"Line number: {exc_traceback.tb_lineno}")
```

### 8. Checking Python Version

```python
import sys

# Method 1: version_info tuple
if sys.version_info >= (3, 8):
    print("Python 3.8 or higher")
else:
    print("Please upgrade Python")
    sys.exit(1)

# Method 2: Named attributes
major, minor = sys.version_info.major, sys.version_info.minor
print(f"Running Python {major}.{minor}")
```

### 9. Recursion Limit Management

```python
import sys

# Check current limit
print(f"Current recursion limit: {sys.getrecursionlimit()}")

# Increase for deep recursion (use carefully!)
sys.setrecursionlimit(2000)

def deep_function(n):
    if n <= 0:
        return 0
    return deep_function(n - 1) + 1

result = deep_function(1500)
```

## Pro Tips

### 💡 Tip 1: Safe Exit in Scripts
Always use `sys.exit()` instead of `exit()` in production code. The `exit()` function is meant for interactive use only.

```python
# Good
import sys
sys.exit(1)

# Avoid in scripts
exit(1)  # Only for interactive shell
```

### 💡 Tip 2: Writing to stderr
Use `sys.stderr` for error messages to separate them from standard output.

```python
import sys

# This allows users to redirect output separately
print("Normal output")  # Goes to stdout
print("Error occurred!", file=sys.stderr)  # Goes to stderr
```

### 💡 Tip 3: Module Reloading
Check if a module is already loaded before importing.

```python
import sys

if 'expensive_module' not in sys.modules:
    import expensive_module
```

### 💡 Tip 4: Unbuffered Output
For real-time logging, flush output immediately.

```python
import sys

print("Processing...", end='', flush=True)
# Or
sys.stdout.flush()
```

### 💡 Tip 5: Virtual Environment Detection
Detect if running in a virtual environment.

```python
import sys

in_virtualenv = hasattr(sys, 'real_prefix') or \
                (hasattr(sys, 'base_prefix') and sys.base_prefix != sys.prefix)

if in_virtualenv:
    print("Running in virtual environment")
```

## Common Pitfalls

⚠️ **Don't modify `sys.path` permanently** - Use virtual environments instead

⚠️ **Be cautious with `setrecursionlimit()`** - Setting it too high can crash Python

⚠️ **`sys.argv[0]` is the script name** - Actual arguments start at index 1

⚠️ **`getsizeof()` only measures direct size** - Doesn't include referenced objects

## Quick Reference Commands

```python
# Get script directory
import sys, os
script_dir = os.path.dirname(os.path.abspath(sys.argv[0]))

# Suppress output temporarily
sys.stdout = open(os.devnull, 'w')

# Restore output
sys.stdout = sys.__stdout__

# Check if running as script or imported
if __name__ == '__main__':
    # Running as script
    pass
```

## Exit Status Codes Convention

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1 | General error |
| 2 | Misuse of shell command |
| 126 | Command cannot execute |
| 127 | Command not found |
| 130 | Terminated by Ctrl+C |

---

**Documentation:** [https://docs.python.org/3/library/sys.html](https://docs.python.org/3/library/sys.html)