# Python OS Library Cheatsheet

## Overview
The `os` module provides a portable way to interact with the operating system. It offers functions for file operations, directory management, process management, and environment variables.

```python
import os
```

---

## Essential Attributes

| Attribute | Description | Example Value |
|-----------|-------------|---------------|
| `os.name` | Name of the operating system | `'posix'` (Linux/Mac), `'nt'` (Windows) |
| `os.sep` | Path separator | `'/'` (Unix), `'\\'` (Windows) |
| `os.pathsep` | Path list separator | `':'` (Unix), `';'` (Windows) |
| `os.linesep` | Line ending | `'\n'` (Unix), `'\r\n'` (Windows) |
| `os.curdir` | Current directory string | `'.'` |
| `os.pardir` | Parent directory string | `'..'` |
| `os.environ` | Environment variables dict | `{'PATH': '...', 'HOME': '...'}` |

---

## Directory Operations

| Method | Description | Returns |
|--------|-------------|---------|
| `os.getcwd()` | Get current working directory | String path |
| `os.chdir(path)` | Change current directory | None |
| `os.listdir(path='.')` | List directory contents | List of filenames |
| `os.mkdir(path)` | Create single directory | None |
| `os.makedirs(path)` | Create directory tree | None |
| `os.rmdir(path)` | Remove empty directory | None |
| `os.removedirs(path)` | Remove empty directory tree | None |
| `os.scandir(path='.')` | Efficient directory iterator | Iterator of DirEntry |

### Examples
```python
# Get and change directories
current = os.getcwd()
print(f"Current: {current}")  # /home/user/project

os.chdir('/tmp')
print(os.getcwd())  # /tmp

# List directory contents
files = os.listdir('.')
print(files)  # ['file1.txt', 'folder1', 'file2.py']

# Create directories
os.mkdir('new_folder')  # Creates single folder
os.makedirs('parent/child/grandchild')  # Creates entire path

# Better way with exist_ok
os.makedirs('my_dir', exist_ok=True)  # Won't error if exists
```

---

## File Operations

| Method | Description | Returns |
|--------|-------------|---------|
| `os.remove(path)` | Delete a file | None |
| `os.rename(src, dst)` | Rename/move file or directory | None |
| `os.replace(src, dst)` | Rename (overwrites destination) | None |
| `os.stat(path)` | Get file status | stat_result object |
| `os.access(path, mode)` | Check file permissions | Boolean |
| `os.chmod(path, mode)` | Change file permissions | None |

### Examples
```python
# Remove files
os.remove('old_file.txt')

# Rename/move files
os.rename('old_name.txt', 'new_name.txt')
os.rename('file.txt', 'folder/file.txt')  # Move to folder

# Check file info
stat_info = os.stat('myfile.txt')
print(f"Size: {stat_info.st_size} bytes")
print(f"Modified: {stat_info.st_mtime}")

# Check permissions
if os.access('file.txt', os.R_OK):
    print("File is readable")
if os.access('file.txt', os.W_OK):
    print("File is writable")
```

---

## Path Operations

| Method | Description | Returns |
|--------|-------------|---------|
| `os.path.exists(path)` | Check if path exists | Boolean |
| `os.path.isfile(path)` | Check if path is file | Boolean |
| `os.path.isdir(path)` | Check if path is directory | Boolean |
| `os.path.join(*paths)` | Join path components | String path |
| `os.path.split(path)` | Split into directory and file | Tuple (dir, file) |
| `os.path.basename(path)` | Get filename from path | String |
| `os.path.dirname(path)` | Get directory from path | String |
| `os.path.abspath(path)` | Get absolute path | String path |
| `os.path.getsize(path)` | Get file size in bytes | Integer |

### Examples
```python
# Build paths safely (cross-platform)
path = os.path.join('folder', 'subfolder', 'file.txt')
# Windows: folder\subfolder\file.txt
# Unix: folder/subfolder/file.txt

# Check existence
if os.path.exists('myfile.txt'):
    if os.path.isfile('myfile.txt'):
        print("It's a file")
    elif os.path.isdir('myfile.txt'):
        print("It's a directory")

# Path manipulation
full_path = '/home/user/documents/report.pdf'
directory = os.path.dirname(full_path)  # /home/user/documents
filename = os.path.basename(full_path)  # report.pdf
dir_path, file_name = os.path.split(full_path)

# Get absolute path
abs_path = os.path.abspath('relative/path.txt')
# /home/user/current_dir/relative/path.txt

# File size
size = os.path.getsize('large_file.dat')
print(f"Size: {size / (1024**2):.2f} MB")
```

---

## Environment Variables

| Method | Description | Returns |
|--------|-------------|---------|
| `os.getenv(key, default=None)` | Get environment variable | String or None |
| `os.environ.get(key, default)` | Get environment variable | String or default |
| `os.environ[key]` | Get environment variable | String (raises KeyError) |
| `os.putenv(key, value)` | Set environment variable | None |
| `os.environ[key] = value` | Set environment variable | None |

### Examples
```python
# Reading environment variables
home = os.getenv('HOME')  # Returns None if not set
path = os.getenv('PATH', '/usr/bin')  # With default value

# Safe reading
user = os.environ.get('USER', 'unknown')

# Setting environment variables
os.environ['MY_VAR'] = 'my_value'
os.environ['DATABASE_URL'] = 'postgresql://localhost/mydb'

# Check if variable exists
if 'DEBUG' in os.environ:
    print("Debug mode enabled")
```

---

## Process Management

| Method | Description | Returns |
|--------|-------------|---------|
| `os.system(command)` | Execute shell command | Exit status |
| `os.getpid()` | Get current process ID | Integer |
| `os.getppid()` | Get parent process ID | Integer |
| `os.kill(pid, signal)` | Send signal to process | None |
| `os.exit(n)` | Exit immediately | No return |

### Examples
```python
# Execute system commands (use subprocess module instead for production)
status = os.system('ls -la')
os.system('echo "Hello World"')

# Process information
pid = os.getpid()
print(f"My PID: {pid}")

parent_pid = os.getppid()
print(f"Parent PID: {parent_pid}")
```

---

## Walking Directory Trees

| Method | Description | Returns |
|--------|-------------|---------|
| `os.walk(top)` | Traverse directory tree | Iterator of tuples |

### Examples
```python
# Walk through all directories and files
for dirpath, dirnames, filenames in os.walk('/path/to/start'):
    print(f"Directory: {dirpath}")
    print(f"Subdirectories: {dirnames}")
    print(f"Files: {filenames}")
    
# Find all Python files
python_files = []
for root, dirs, files in os.walk('.'):
    for file in files:
        if file.endswith('.py'):
            full_path = os.path.join(root, file)
            python_files.append(full_path)

# Calculate total directory size
total_size = 0
for dirpath, dirnames, filenames in os.walk('/my/folder'):
    for filename in filenames:
        filepath = os.path.join(dirpath, filename)
        total_size += os.path.getsize(filepath)
print(f"Total size: {total_size / (1024**2):.2f} MB")
```

---

## Practical Examples

### Example 1: Safe File Operations
```python
import os

def safe_create_file(directory, filename):
    """Create a file in a directory, creating the directory if needed."""
    # Ensure directory exists
    os.makedirs(directory, exist_ok=True)
    
    # Create full path
    filepath = os.path.join(directory, filename)
    
    # Check if file already exists
    if os.path.exists(filepath):
        print(f"Warning: {filepath} already exists")
        return None
    
    # Create the file
    with open(filepath, 'w') as f:
        f.write("Initial content")
    
    return filepath

# Usage
path = safe_create_file('logs/2024', 'app.log')
```

### Example 2: Backup Script
```python
import os
import shutil
from datetime import datetime

def backup_directory(source_dir, backup_root):
    """Create timestamped backup of directory."""
    # Create backup directory with timestamp
    timestamp = datetime.now().strftime('%Y%m%d_%H%M%S')
    backup_name = f"backup_{timestamp}"
    backup_path = os.path.join(backup_root, backup_name)
    
    # Ensure backup root exists
    os.makedirs(backup_root, exist_ok=True)
    
    # Copy directory
    shutil.copytree(source_dir, backup_path)
    
    print(f"Backup created: {backup_path}")
    return backup_path

# Usage
backup_directory('/home/user/important_data', '/backups')
```

### Example 3: File Organizer
```python
import os
import shutil

def organize_files_by_extension(directory):
    """Organize files into subdirectories by extension."""
    for filename in os.listdir(directory):
        filepath = os.path.join(directory, filename)
        
        # Skip directories
        if os.path.isdir(filepath):
            continue
        
        # Get file extension
        _, ext = os.path.splitext(filename)
        ext = ext.lower().strip('.')
        
        # Create extension folder
        ext_folder = os.path.join(directory, ext if ext else 'no_extension')
        os.makedirs(ext_folder, exist_ok=True)
        
        # Move file
        new_path = os.path.join(ext_folder, filename)
        shutil.move(filepath, new_path)
        print(f"Moved: {filename} -> {ext_folder}/")

# Usage
organize_files_by_extension('/home/user/Downloads')
```

---

## Important Tips

### ✅ Best Practices
1. **Use `os.path.join()`** for cross-platform compatibility
2. **Use `exist_ok=True`** with `makedirs()` to avoid errors
3. **Always check existence** before operating on files
4. **Use `os.path` methods** instead of string manipulation
5. **Prefer `pathlib`** for modern Python (3.4+) projects
6. **Use `with` statements** when opening files
7. **Handle exceptions** for file operations

### ⚠️ Common Pitfalls
- Don't use `os.system()` for production code (use `subprocess` instead)
- `os.rename()` fails if destination exists on Windows (use `os.replace()`)
- `os.remove()` only works on files, use `os.rmdir()` for directories
- Environment variable changes with `os.environ` only affect current process
- Be careful with `os.chdir()` in multi-threaded applications

### 🔒 Security Notes
- Always validate user input before using in file paths
- Be cautious with `os.system()` and shell commands (injection risks)
- Use absolute paths when possible for clarity
- Check permissions before attempting operations

---

## Alternative: Modern `pathlib` Module

For Python 3.4+, consider using `pathlib` for more object-oriented file operations:

```python
from pathlib import Path

# Modern equivalent operations
current = Path.cwd()
home = Path.home()

# Path joining
path = Path('folder') / 'subfolder' / 'file.txt'

# Check existence
if path.exists():
    if path.is_file():
        print("It's a file")

# Create directories
Path('new/nested/dir').mkdir(parents=True, exist_ok=True)

# Read/write files
path.write_text('content')
content = path.read_text()

# Iterate over files
for file in Path('.').glob('*.txt'):
    print(file)
```

---

## Quick Reference Card

```python
# Directory operations
os.getcwd()                    # Get current directory
os.chdir(path)                 # Change directory
os.listdir(path)               # List contents
os.makedirs(path, exist_ok=True)  # Create directories

# File operations
os.path.exists(path)           # Check existence
os.path.isfile(path)           # Is file?
os.path.isdir(path)            # Is directory?
os.path.join(a, b, c)          # Join paths safely
os.remove(path)                # Delete file
os.rename(old, new)            # Rename/move

# Environment
os.getenv('VAR', 'default')    # Get env variable
os.environ['VAR'] = 'value'    # Set env variable

# Walking
for root, dirs, files in os.walk(path):
    # Process each directory
    pass
```
