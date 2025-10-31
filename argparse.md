# Python argparse Cheatsheet

## Overview
`argparse` is Python's standard library for parsing command-line arguments. It automatically generates help messages and handles errors when users provide invalid arguments.

## Basic Usage

```python
import argparse

# Create parser
parser = argparse.ArgumentParser(description='Program description')

# Add arguments
parser.add_argument('filename', help='Input file name')
parser.add_argument('-v', '--verbose', action='store_true', help='Verbose output')

# Parse arguments
args = parser.parse_args()

# Access arguments
print(args.filename)
print(args.verbose)
```

---

## ArgumentParser Constructor

### Key Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `prog` | str | `sys.argv[0]` | Program name (shown in help) |
| `description` | str | None | Program description (before arguments) |
| `epilog` | str | None | Text after argument help |
| `formatter_class` | class | `HelpFormatter` | Help message formatting class |
| `add_help` | bool | True | Add -h/--help option |
| `allow_abbrev` | bool | True | Allow long options to be abbreviated |

### Example

```python
parser = argparse.ArgumentParser(
    prog='MyApp',
    description='Does something useful',
    epilog='Thanks for using %(prog)s!',
    formatter_class=argparse.RawDescriptionHelpFormatter
)
```

---

## add_argument() Method

### Essential Parameters

| Parameter | Type | Description | Example |
|-----------|------|-------------|---------|
| `name or flags` | str | Positional or option name | `'input'` or `'-f', '--file'` |
| `action` | str | Action when argument encountered | `'store'`, `'store_true'`, `'append'` |
| `nargs` | int/str | Number of arguments | `2`, `'*'`, `'+'`, `'?'` |
| `const` | any | Constant value for action/nargs | `42`, `'default.txt'` |
| `default` | any | Default value if not provided | `None`, `[]`, `0` |
| `type` | callable | Type converter function | `int`, `float`, `open`, custom function |
| `choices` | list | Allowed values | `['rock', 'paper', 'scissors']` |
| `required` | bool | Whether optional arg is required | `True`, `False` |
| `help` | str | Help message | `'Input file to process'` |
| `metavar` | str | Name in usage messages | `'FILE'`, `'N'` |
| `dest` | str | Attribute name in parsed args | `'output_file'` |

---

## Action Types

| Action | Description | Example Usage |
|--------|-------------|---------------|
| `'store'` | Store the value (default) | `--output file.txt` → `args.output = 'file.txt'` |
| `'store_const'` | Store a constant value | `--debug` → `args.debug = True` |
| `'store_true'` | Store True if present | `--verbose` → `args.verbose = True` |
| `'store_false'` | Store False if present | `--no-cache` → `args.cache = False` |
| `'append'` | Append values to a list | `--add 1 --add 2` → `args.add = [1, 2]` |
| `'append_const'` | Append constant to list | Multiple flags build a list |
| `'count'` | Count occurrences | `-vvv` → `args.v = 3` |
| `'help'` | Show help and exit | Built-in with `-h` |
| `'version'` | Show version and exit | `--version` |
| `'extend'` | Extend list (3.8+) | `--files a b --files c` → `[a,b,c]` |

---

## nargs Specifiers

| Value | Description | Example |
|-------|-------------|---------|
| `N` (integer) | Exactly N arguments | `nargs=2` → requires 2 values |
| `'?'` | 0 or 1 argument | Optional single value |
| `'*'` | 0 or more arguments | Any number of values |
| `'+'` | 1 or more arguments | At least one value required |
| `argparse.REMAINDER` | All remaining arguments | Capture everything left |

---

## Formatter Classes

| Class | Purpose |
|-------|---------|
| `HelpFormatter` | Default formatting |
| `RawDescriptionHelpFormatter` | Keep description formatting as-is |
| `RawTextHelpFormatter` | Keep all text formatting |
| `ArgumentDefaultsHelpFormatter` | Show default values in help |
| `MetavarTypeHelpFormatter` | Use type name as metavar |

---

## Concrete Examples

### Example 1: Basic File Processing Script

```python
import argparse

parser = argparse.ArgumentParser(description='Process a file')
parser.add_argument('input', help='Input file path')
parser.add_argument('output', help='Output file path')
parser.add_argument('-v', '--verbose', action='store_true', 
                    help='Enable verbose output')
parser.add_argument('-f', '--format', choices=['json', 'xml', 'csv'],
                    default='json', help='Output format')

args = parser.parse_args()

print(f"Processing {args.input} → {args.output}")
print(f"Format: {args.format}, Verbose: {args.verbose}")
```

**Usage:**
```bash
python script.py input.txt output.txt --format csv -v
```

---

### Example 2: Multiple Values and Types

```python
parser = argparse.ArgumentParser(description='Calculate statistics')
parser.add_argument('numbers', type=float, nargs='+',
                    help='Numbers to process (space-separated)')
parser.add_argument('--precision', type=int, default=2,
                    help='Decimal precision (default: 2)')

args = parser.parse_args()

avg = sum(args.numbers) / len(args.numbers)
print(f"Average: {avg:.{args.precision}f}")
```

**Usage:**
```bash
python script.py 1.5 2.7 3.9 --precision 3
```

---

### Example 3: Mutually Exclusive Groups

```python
parser = argparse.ArgumentParser(description='File operations')
group = parser.add_mutually_exclusive_group(required=True)
group.add_argument('--create', action='store_true', help='Create file')
group.add_argument('--delete', action='store_true', help='Delete file')
group.add_argument('--update', action='store_true', help='Update file')

parser.add_argument('filename', help='Target file')

args = parser.parse_args()
```

**Usage:** Only one of `--create`, `--delete`, or `--update` allowed.

---

### Example 4: Subcommands (Like git)

```python
parser = argparse.ArgumentParser(prog='myapp')
subparsers = parser.add_subparsers(dest='command', help='Available commands')

# Add subcommand
add_parser = subparsers.add_parser('add', help='Add items')
add_parser.add_argument('items', nargs='+', help='Items to add')

# Remove subcommand
remove_parser = subparsers.add_parser('remove', help='Remove items')
remove_parser.add_argument('items', nargs='+', help='Items to remove')

args = parser.parse_args()

if args.command == 'add':
    print(f"Adding: {args.items}")
elif args.command == 'remove':
    print(f"Removing: {args.items}")
```

**Usage:**
```bash
python script.py add item1 item2
python script.py remove item3
```

---

### Example 5: Custom Type Validation

```python
def validate_port(value):
    ivalue = int(value)
    if ivalue < 1 or ivalue > 65535:
        raise argparse.ArgumentTypeError(f"{value} is not a valid port (1-65535)")
    return ivalue

parser = argparse.ArgumentParser()
parser.add_argument('--port', type=validate_port, default=8080,
                    help='Port number (1-65535)')

args = parser.parse_args()
print(f"Using port: {args.port}")
```

---

### Example 6: File Handling

```python
parser = argparse.ArgumentParser()
parser.add_argument('-i', '--input', type=argparse.FileType('r'),
                    default='-', help='Input file (default: stdin)')
parser.add_argument('-o', '--output', type=argparse.FileType('w'),
                    default='-', help='Output file (default: stdout)')

args = parser.parse_args()

# Files are already opened!
content = args.input.read()
args.output.write(content.upper())

args.input.close()
args.output.close()
```

---

### Example 7: Counting Flags

```python
parser = argparse.ArgumentParser()
parser.add_argument('-v', '--verbose', action='count', default=0,
                    help='Increase verbosity (can be repeated)')

args = parser.parse_args()

if args.verbose == 0:
    print("Silent mode")
elif args.verbose == 1:
    print("Normal verbosity")
elif args.verbose >= 2:
    print("Maximum verbosity")
```

**Usage:**
```bash
python script.py -v        # verbose = 1
python script.py -vv       # verbose = 2
python script.py -vvv      # verbose = 3
```

---

### Example 8: Append Multiple Values

```python
parser = argparse.ArgumentParser()
parser.add_argument('--include', action='append',
                    help='Add include path (can be used multiple times)')

args = parser.parse_args()
print(f"Include paths: {args.include}")
```

**Usage:**
```bash
python script.py --include /usr/lib --include /opt/lib
# Result: args.include = ['/usr/lib', '/opt/lib']
```

---

## Important Methods

| Method | Description | Example |
|--------|-------------|---------|
| `parse_args()` | Parse command-line arguments | `args = parser.parse_args()` |
| `parse_args(args)` | Parse from a list instead of sys.argv | `parser.parse_args(['--help'])` |
| `add_argument()` | Add argument to parser | See examples above |
| `add_mutually_exclusive_group()` | Create mutually exclusive argument group | Example 3 above |
| `add_subparsers()` | Add subcommand support | Example 4 above |
| `set_defaults()` | Set default values for arguments | `parser.set_defaults(func=my_func)` |
| `get_default()` | Get default value for argument | `parser.get_default('verbose')` |
| `print_help()` | Print help message | `parser.print_help()` |
| `parse_known_args()` | Parse known args, ignore unknown | Returns tuple: `(args, unknown)` |

---

## Pro Tips & Best Practices

### 1. **Use ArgumentDefaultsHelpFormatter**
Shows default values automatically in help text:
```python
parser = argparse.ArgumentParser(
    formatter_class=argparse.ArgumentDefaultsHelpFormatter
)
```

### 2. **Namespace for Organization**
Group related arguments:
```python
args = parser.parse_args()
# Access via namespace: args.server_host, args.server_port, etc.
```

### 3. **Version Information**
Add version display:
```python
parser.add_argument('--version', action='version', version='%(prog)s 2.0')
```

### 4. **Boolean Flags Pattern**
Create complementary flags:
```python
parser.add_argument('--feature', dest='feature', action='store_true')
parser.add_argument('--no-feature', dest='feature', action='store_false')
parser.set_defaults(feature=True)
```

### 5. **Environment Variable Defaults**
```python
import os
parser.add_argument('--api-key', 
                    default=os.getenv('API_KEY'),
                    help='API key (default: from API_KEY env var)')
```

### 6. **Validate Early**
Use `type` parameter for immediate validation:
```python
parser.add_argument('--count', type=int, help='Must be integer')
```

### 7. **Use dest for Cleaner Names**
```python
parser.add_argument('--input-file', dest='input', help='Input file')
# Access as: args.input (not args.input_file)
```

### 8. **Testing Your Argument Parser**
```python
# In tests, pass arguments directly
args = parser.parse_args(['--verbose', 'file.txt'])
```

### 9. **Partial Parsing**
When you need to ignore some arguments:
```python
args, unknown = parser.parse_known_args()
# Process args, pass unknown to another system
```

### 10. **Complex Validation**
For complex validation, use custom types:
```python
def valid_date(s):
    try:
        return datetime.strptime(s, "%Y-%m-%d")
    except ValueError:
        raise argparse.ArgumentTypeError(f"Invalid date: {s}")

parser.add_argument('--date', type=valid_date)
```

---

## Common Patterns

### Configuration Priority Pattern
```python
# Priority: CLI args > config file > defaults
import json

parser = argparse.ArgumentParser()
parser.add_argument('--config', type=str, help='Config file')
parser.add_argument('--host', default='localhost')
parser.add_argument('--port', type=int, default=8080)

args = parser.parse_args()

if args.config:
    with open(args.config) as f:
        config = json.load(f)
    # Apply config only if not specified on CLI
    for key, value in config.items():
        if getattr(args, key, None) == parser.get_default(key):
            setattr(args, key, value)
```

### Debugging Tip
```python
# See what was parsed
print(vars(args))  # Convert Namespace to dictionary
```

---

## Quick Reference

```python
# Positional argument
parser.add_argument('name')

# Optional argument
parser.add_argument('-n', '--name')

# Flag (boolean)
parser.add_argument('-v', action='store_true')

# Multiple values
parser.add_argument('files', nargs='+')

# Choice restriction
parser.add_argument('--format', choices=['json', 'xml'])

# Type conversion
parser.add_argument('--count', type=int)

# Default value
parser.add_argument('--timeout', type=int, default=30)

# Required optional argument
parser.add_argument('--config', required=True)

# Custom help message
parser.add_argument('file', help='Input file path')
```