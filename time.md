# time - Time Operations Library

## Overview
The `time` module provides time-related functions for accessing time, formatting dates, and measuring performance.

---

## Module Functions

### Time Access Functions

| Function | Parameters | Return | Usage |
|----------|-----------|--------|-------|
| `time()` | None | Float (seconds since Epoch) | `current = time.time()` |
| `time_ns()` | None | Int (nanoseconds since Epoch) | `nano = time.time_ns()` |
| `ctime()` | `secs=None` | String (formatted time) | `time_str = time.ctime()` |
| `gmtime()` | `secs=None` | struct_time (UTC) | `utc = time.gmtime()` |
| `localtime()` | `secs=None` | struct_time (local time) | `local = time.localtime()` |
| `mktime()` | `t` | Float (seconds since Epoch) | `secs = time.mktime(t)` |
| `asctime()` | `t=None` | String | `str_time = time.asctime()` |

### Formatting Functions

| Function | Parameters | Return | Usage |
|----------|-----------|--------|-------|
| `strftime()` | `format, t=None` | String | `time.strftime("%Y-%m-%d %H:%M:%S")` |
| `strptime()` | `string, format` | struct_time | `t = time.strptime("2025-10-31", "%Y-%m-%d")` |

### Delay Functions

| Function | Parameters | Return | Usage |
|----------|-----------|--------|-------|
| `sleep()` | `secs` | None | `time.sleep(2.5)` |

### Performance Measurement Functions

| Function | Parameters | Return | Usage |
|----------|-----------|--------|-------|
| `perf_counter()` | None | Float (seconds) | `start = time.perf_counter()` |
| `perf_counter_ns()` | None | Int (nanoseconds) | `start = time.perf_counter_ns()` |
| `process_time()` | None | Float (CPU seconds) | `cpu = time.process_time()` |
| `process_time_ns()` | None | Int (CPU nanoseconds) | `cpu = time.process_time_ns()` |
| `monotonic()` | None | Float (seconds) | `mono = time.monotonic()` |
| `monotonic_ns()` | None | Int (nanoseconds) | `mono = time.monotonic_ns()` |

### Clock Functions (Unix)

| Function | Parameters | Return | Usage |
|----------|-----------|--------|-------|
| `clock_gettime()` | `clk_id` | Float (seconds) | `t = time.clock_gettime(time.CLOCK_MONOTONIC)` |
| `clock_getres()` | `clk_id` | Float (resolution) | `res = time.clock_getres(time.CLOCK_MONOTONIC)` |

---

## Module Attributes

| Attribute | Type | Description | Usage |
|-----------|------|-------------|-------|
| `timezone` | Int | UTC offset in seconds (non-DST) | `offset_hours = time.timezone / 3600` |
| `altzone` | Int | UTC offset in seconds (DST) | `dst_offset = time.altzone / 3600` |
| `daylight` | Int | Non-zero if DST defined | `if time.daylight: print("DST defined")` |
| `tzname` | Tuple | Timezone names (standard, DST) | `print(time.tzname)` |

---

## Clock Constants (Unix)

| Constant | Description | Usage |
|----------|-------------|-------|
| `CLOCK_REALTIME` | System-wide clock | `time.clock_gettime(time.CLOCK_REALTIME)` |
| `CLOCK_MONOTONIC` | Monotonic clock | `time.clock_gettime(time.CLOCK_MONOTONIC)` |
| `CLOCK_PROCESS_CPUTIME_ID` | Process CPU time | `time.clock_gettime(time.CLOCK_PROCESS_CPUTIME_ID)` |
| `CLOCK_THREAD_CPUTIME_ID` | Thread CPU time | `time.clock_gettime(time.CLOCK_THREAD_CPUTIME_ID)` |

---

## struct_time Object

The `struct_time` object is returned by several time functions.

| Attribute | Index | Type | Range | Usage |
|-----------|-------|------|-------|-------|
| `tm_year` | 0 | int | 1970-... | `t.tm_year` |
| `tm_mon` | 1 | int | 1-12 | `t.tm_mon` |
| `tm_mday` | 2 | int | 1-31 | `t.tm_mday` |
| `tm_hour` | 3 | int | 0-23 | `t.tm_hour` |
| `tm_min` | 4 | int | 0-59 | `t.tm_min` |
| `tm_sec` | 5 | int | 0-61 | `t.tm_sec` |
| `tm_wday` | 6 | int | 0-6 (Mon=0) | `t.tm_wday` |
| `tm_yday` | 7 | int | 1-366 | `t.tm_yday` |
| `tm_isdst` | 8 | int | -1, 0, 1 | `t.tm_isdst` |

```python
t = time.localtime()
print(f"Year: {t.tm_year}")
print(f"Month: {t.tm_mon}")
print(f"Day: {t.tm_mday}")
print(f"Hour: {t.tm_hour}")
```

---

## strftime Format Codes

| Code | Meaning | Example | Usage |
|------|---------|---------|-------|
| `%Y` | Year (4 digits) | 2025 | `time.strftime("%Y")` |
| `%y` | Year (2 digits) | 25 | `time.strftime("%y")` |
| `%m` | Month (01-12) | 10 | `time.strftime("%m")` |
| `%B` | Month name | October | `time.strftime("%B")` |
| `%b` | Month abbr | Oct | `time.strftime("%b")` |
| `%d` | Day (01-31) | 31 | `time.strftime("%d")` |
| `%A` | Weekday name | Friday | `time.strftime("%A")` |
| `%a` | Weekday abbr | Fri | `time.strftime("%a")` |
| `%H` | Hour 24h (00-23) | 14 | `time.strftime("%H")` |
| `%I` | Hour 12h (01-12) | 02 | `time.strftime("%I")` |
| `%M` | Minute (00-59) | 30 | `time.strftime("%M")` |
| `%S` | Second (00-59) | 45 | `time.strftime("%S")` |
| `%p` | AM/PM | PM | `time.strftime("%p")` |
| `%f` | Microsecond | 123456 | `datetime.strftime("%f")` (datetime only) |
| `%z` | UTC offset | +0530 | `time.strftime("%z")` |
| `%Z` | Timezone name | EST | `time.strftime("%Z")` |
| `%j` | Day of year (001-366) | 304 | `time.strftime("%j")` |
| `%U` | Week number (Sun start) | 43 | `time.strftime("%U")` |
| `%W` | Week number (Mon start) | 43 | `time.strftime("%W")` |
| `%c` | Locale date/time | Fri Oct 31 14:30:45 2025 | `time.strftime("%c")` |
| `%x` | Locale date | 10/31/25 | `time.strftime("%x")` |
| `%X` | Locale time | 14:30:45 | `time.strftime("%X")` |
| `%%` | Literal % | % | `time.strftime("%%")` |

---

## Complete Examples

### Basic Time Operations
```python
import time

# Current timestamp
current = time.time()
print(f"Timestamp: {current}")

# Human-readable format
print(f"Current time: {time.ctime()}")

# Detailed time info
now = time.localtime()
print(f"Year: {now.tm_year}")
print(f"Month: {now.tm_mon}")
print(f"Day: {now.tm_mday}")
print(f"Hour: {now.tm_hour}")
print(f"Minute: {now.tm_min}")
print(f"Second: {now.tm_sec}")
```

### Date/Time Formatting
```python
import time

now = time.localtime()

# Various formats
print(time.strftime("%Y-%m-%d", now))                    # 2025-10-31
print(time.strftime("%d/%m/%Y", now))                    # 31/10/2025
print(time.strftime("%B %d, %Y", now))                   # October 31, 2025
print(time.strftime("%I:%M:%S %p", now))                 # 02:30:45 PM
print(time.strftime("%A, %B %d, %Y at %I:%M %p", now))  # Friday, October 31, 2025 at 02:30 PM
print(time.strftime("%Y-%m-%d %H:%M:%S", now))          # 2025-10-31 14:30:45
```

### Parsing Date Strings
```python
import time

# Parse various formats
date_str1 = "2025-10-31 14:30:45"
parsed1 = time.strptime(date_str1, "%Y-%m-%d %H:%M:%S")
print(f"Parsed year: {parsed1.tm_year}")

date_str2 = "October 31, 2025"
parsed2 = time.strptime(date_str2, "%B %d, %Y")
print(f"Parsed month: {parsed2.tm_mon}")

# Convert back to timestamp
timestamp = time.mktime(parsed1)
print(f"Timestamp: {timestamp}")
```

### Performance Timing
```python
import time

# Measure execution time
start = time.perf_counter()

# Simulate work
total = 0
for i in range(1000000):
    total += i

end = time.perf_counter()
print(f"Execution time: {end - start:.6f} seconds")

# High precision timing
start_ns = time.perf_counter_ns()
# ... some operation ...
end_ns = time.perf_counter_ns()
print(f"Nanoseconds elapsed: {end_ns - start_ns}")
```

### CPU Time Measurement
```python
import time

# Measure CPU time (excludes sleep time)
cpu_start = time.process_time()

# CPU work
result = sum(i**2 for i in range(1000000))

# Sleep doesn't count toward CPU time
time.sleep(1)

cpu_end = time.process_time()
print(f"CPU time: {cpu_end - cpu_start:.6f} seconds")
```

### Countdown Timer
```python
import time

def countdown(seconds):
    """Simple countdown timer"""
    for i in range(seconds, 0, -1):
        mins, secs = divmod(i, 60)
        timeformat = f"{mins:02d}:{secs:02d}"
        print(timeformat, end='\r')
        time.sleep(1)
    print("Time's up!      ")

countdown(10)
```

### Stopwatch
```python
import time

def stopwatch():
    """Simple stopwatch"""
    input("Press Enter to start...")
    start = time.time()
    
    input("Press Enter to stop...")
    end = time.time()
    
    elapsed = end - start
    print(f"\nElapsed time: {elapsed:.2f} seconds")

stopwatch()
```

### Rate Limiting
```python
import time

class RateLimiter:
    def __init__(self, calls_per_second):
        self.min_interval = 1.0 / calls_per_second
        self.last_called = 0
    
    def wait(self):
        elapsed = time.time() - self.last_called
        if elapsed < self.min_interval:
            time.sleep(self.min_interval - elapsed)
        self.last_called = time.time()

# Usage
limiter = RateLimiter(2)  # 2 calls per second

for i in range(5):
    limiter.wait()
    print(f"Call {i+1} at {time.strftime('%H:%M:%S')}")
```

### Benchmarking
```python
import time

def benchmark(func, iterations=1000):
    """Benchmark a function"""
    start = time.perf_counter()
    
    for _ in range(iterations):
        func()
    
    end = time.perf_counter()
    total_time = end - start
    avg_time = total_time / iterations
    
    print(f"Total time: {total_time:.6f} seconds")
    print(f"Average time: {avg_time:.9f} seconds")
    print(f"Calls per second: {iterations / total_time:.2f}")

# Test function
def test_function():
    return sum(range(100))

benchmark(test_function, 10000)
```

### Time Zone Conversion
```python
import time

# Get current time in different representations
local_time = time.localtime()
utc_time = time.gmtime()

print("Local time:")
print(time.strftime("%Y-%m-%d %H:%M:%S %Z", local_time))

print("\nUTC time:")
print(time.strftime("%Y-%m-%d %H:%M:%S %Z", utc_time))

# Timezone info
print(f"\nTimezone offset: {time.timezone / 3600} hours")
print(f"DST offset: {time.altzone / 3600} hours")
print(f"Timezone names: {time.tzname}")
```

### Sleep with Progress
```python
import time

def sleep_with_progress(seconds, step=0.1):
    """Sleep with progress indicator"""
    steps = int(seconds / step)
    for i in range(steps + 1):
        progress = (i / steps) * 100
        bar_length = int(progress / 2)
        bar = '█' * bar_length + '░' * (50 - bar_length)
        print(f'\rProgress: [{bar}] {progress:.1f}%', end='')
        time.sleep(step)
    print()

sleep_with_progress(5)
```

---

## Best Practices

1. **Use appropriate timing functions**:
   - `perf_counter()` - For measuring elapsed time
   - `process_time()` - For measuring CPU time
   - `monotonic()` - For timeouts and intervals
   - `time()` - For timestamps and dates

2. **Avoid deprecated functions**:
   - Don't use `time.clock()` (removed in Python 3.8)

3. **Handle time zones carefully**:
   - Use `gmtime()` for UTC
   - Use `localtime()` for local time

4. **Format dates consistently**:
   - Use ISO 8601 format: `%Y-%m-%d %H:%M:%S`

5. **Be careful with sleep()**:
   - May sleep longer than requested
   - Not suitable for precise timing

6. **Use high-resolution timers**:
   - Use `perf_counter()` instead of `time()` for benchmarks
   - Use `_ns()` variants for nanosecond precision
