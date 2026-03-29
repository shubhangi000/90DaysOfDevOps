# Day 18 – Shell Scripting: Functions & Intermediate Concepts

---

## 🔹 Task 1: Basic Functions

### 📄 `functions.sh`

A simple script demonstrating basic function definition and usage with parameters.

```bash
#!/bin/bash

greet() {
    echo "Hello, $1!"
}

add() {
    sum=$(( $1 + $2 ))
    echo "Sum: $sum"
}

greet "Shubhangi"
add 10 20
```

**How it works:**
- `greet()` - Function that takes one parameter and prints a greeting
- `add()` - Function that takes two parameters, adds them, and displays the result
- `$1, $2` - Positional parameters (arguments passed to the function)

### ✅ Expected Output

```
Hello, Shubhangi!
Sum: 30
```

---

## 🔹 Task 2: Functions with System Commands

### 📄 `disk_check.sh`

A script that encapsulates system commands into reusable functions to check disk and memory usage.

```bash
#!/bin/bash

check_disk() {
    echo "Disk Usage:"
    df -h /
}

check_memory() {
    echo "Memory Usage:"
    free -h
}

echo "===== System Check ====="
check_disk
echo ""
check_memory
```

**What each function does:**
- `check_disk()` - Displays disk usage of the root filesystem using `df -h`
- `check_memory()` - Shows memory and swap usage using `free -h`
- Functions are called sequentially in the main script flow

### ✅ Sample Output

```
===== System Check =====
Disk Usage:
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        50G   20G   28G  42% /

Memory Usage:
              total        used        free
Mem:           7.7G        3.2G        2.1G
```

---

## 🔹 Task 3: Strict Mode (`set -euo pipefail`)

### 📄 `strict_demo.sh`

A demonstration of strict mode that enforces error handling and prevents silent failures.

```bash
#!/bin/bash
set -euo pipefail

echo "Demo of strict mode"

echo "Value: $UNDEFINED_VAR"

false

cat file_that_does_not_exist | grep "test"
```

### ✅ Expected Output

```
Demo of strict mode
./strict_demo.sh: line 5: UNDEFINED_VAR: unbound variable
```

**What happens:**
- Line 1: `set -euo pipefail` enables strict mode
- Line 5: Script stops immediately when `$UNDEFINED_VAR` is accessed (due to `-u`)
- Lines after are never executed

---

## 📋 Deep Dive: `set -euo pipefail` Explained

### **`set -e` (Exit on Error)**

```bash
set -e
```

**Behavior:** The script exits immediately if any command returns a non-zero exit status (failure).

**Example:**
```bash
#!/bin/bash
set -e

echo "Starting..."
false  # Returns exit status 1
echo "This line never executes"
```

**Output:**
```
Starting...
```

**Why it matters:** Prevents the script from continuing after errors, which could cause cascading failures.

---

### **`set -u` (Error on Undefined Variables)**

```bash
set -u
```

**Behavior:** The script throws an error when you reference an undefined variable.

**Example:**
```bash
#!/bin/bash
set -u

echo "Value: $UNDEFINED_VAR"
```

**Output:**
```
./script.sh: line 3: UNDEFINED_VAR: unbound variable
```

**Why it matters:** Catches typos in variable names that would otherwise silently expand to empty strings.

---

### **`set -o pipefail` (Pipeline Failure Detection)**

```bash
set -o pipefail
```

**Behavior:** If any command in a pipeline fails, the entire pipeline is marked as failed.

**Example Without `pipefail`:**
```bash
#!/bin/bash

false | echo "This still succeeds"
echo "Exit status: $?"
```

**Output:**
```
This still succeeds
Exit status: 0  ❌ Wrong! false failed but pipeline succeeded
```

**Example With `pipefail`:**
```bash
#!/bin/bash
set -o pipefail

false | echo "This still succeeds"
echo "This line never executes"
```

**Output:**
```
This still succeeds
./script.sh: line 4: false: command not found
```

**Why it matters:** Ensures you catch errors in intermediate pipeline stages, not just the final command.

---

### 💡 Combined Impact of `set -euo pipefail`

| Flag | Effect | Risk Prevented |
|------|--------|---|
| `-e` | Exit on command failure | Silent continuation after errors |
| `-u` | Error on undefined variables | Variable typos causing unexpected behavior |
| `-o pipefail` | Fail if any pipeline stage fails | Hidden failures in pipes |

**Best Practice:** Add `set -euo pipefail` at the top of every production bash script!

---

## 🔹 Task 4: Local Variables

### 📄 `local_demo.sh`

Demonstrates the difference between local and global variables in bash functions.

```bash
#!/bin/bash

demo_local() {
    local var="I am local"
    echo $var
}

demo_global() {
    var="I am global"
}

demo_local
echo "Outside function: ${var:-Not Defined}"

demo_global
echo "Outside after global: $var"
```

**Function breakdown:**
- `demo_local()` - Declares `var` as local using `local` keyword. Scope is limited to the function.
- `demo_global()` - Assigns to `var` without `local`. This creates a global variable accessible outside the function.
- `${var:-Not Defined}` - Parameter expansion that prints "Not Defined" if `var` is unset

### ✅ Expected Output

```
I am local
Outside function: Not Defined
Outside after global: I am global
```

**Key takeaway:** Always use `local` in functions to avoid polluting the global namespace!

---

## 🔹 Task 5: System Info Reporter (Production-Ready)

### 📄 `system_info.sh`

A comprehensive system monitoring script that demonstrates professional script structure.

```bash
#!/bin/bash
set -euo pipefail

print_header() {
    echo "=============================="
    echo "$1"
    echo "=============================="
}

system_info() {
    print_header "System Information"
    echo "Hostname: $(hostname)"
    echo "OS: $(uname -a)"
}

uptime_info() {
    print_header "Uptime"
    uptime
}

disk_usage() {
    print_header "Top 5 Disk Usage"
    du -h / 2>/dev/null | sort -rh | head -5
}

memory_usage() {
    print_header "Memory Usage"
    free -h
}

cpu_usage() {
    print_header "Top 5 CPU Processes"
    ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%cpu | head -6
}

main() {
    system_info
    uptime_info
    disk_usage
    memory_usage
    cpu_usage
}

main
```

**Architecture breakdown:**
- `print_header()` - Reusable utility function for consistent formatting
- `system_info()` - Displays hostname and OS information
- `uptime_info()` - Shows system uptime
- `disk_usage()` - Lists top 5 disk usage locations (redirects errors to `/dev/null`)
- `memory_usage()` - Shows RAM and swap usage
- `cpu_usage()` - Lists top CPU-consuming processes
- `main()` - Orchestrates all functions in logical order

### ✅ Sample Output

```
==============================
System Information
==============================
Hostname: my-machine
OS: Linux my-machine 5.10.0-8-generic #1 SMP Debian 5.10.46-1 (2021-07-01) x86_64 GNU/Linux

==============================
Uptime
==============================
 10:45:23 up 2 days, 3:12, 1 user, load average: 0.45, 0.38, 0.35

==============================
Top 5 Disk Usage
==============================
50G	/
30G	/var
15G	/home
8G	/usr
4G	/opt

==============================
Memory Usage
==============================
              total        used        free
Mem:           7.7G        3.2G        2.1G
Swap:          2.0G        512M        1.5G

==============================
Top 5 CPU Processes
==============================
PID  PPID  CMD                    %MEM %CPU
 850    1   /usr/bin/python3       2.5 45.2
 1200   850 nginx: worker process  1.2 12.3
 2100   1   /usr/bin/node          3.8  8.5
 2500   2100 bash                   0.5  5.2
 3000   1   sshd                    0.3  1.5
```

---

## 🚀 What I Learned Today

### ✅ Core Concepts Mastered

1. **Functions are fundamental** - Create modular, reusable code blocks instead of repeating commands
2. **Strict mode prevents disasters** - `set -euo pipefail` should be in every production script
3. **Local variables prevent chaos** - Always use `local` in functions to avoid global namespace pollution
4. **Structure matters** - A `main()` function orchestrating helper functions is a professional pattern
5. **System scripting is powerful** - Bash can easily aggregate system information into useful reports

### 📚 Practical Applications

- **Monitoring scripts** - Automated system health checks
- **Automation pipelines** - Chaining operations safely
- **Configuration management** - Repeatable, testable deployments
- **Data processing** - Text manipulation and piping

### 🛡️ Best Practices Adopted

```bash
#!/bin/bash
set -euo pipefail

# Always use functions for organization
my_function() {
    local var="local scope"  # Always use local
    echo "$var"
}

# Central orchestration
main() {
    my_function
    # other calls
}

main "$@"  # Pass arguments if needed
```

---

## 🔥 Key Takeaways

**From basic commands to structured scripts:** Today marked a transition from writing simple bash one-liners to building production-ready scripts with proper error handling, function organization, and system integration.

**The power of `set -euo pipefail`:** This three-part safety mechanism transforms bash from a "hope it works" language to one where failures are explicit and handled properly.

**Modularity through functions:** Breaking scripts into focused functions makes them testable, readable, and maintainable—the hallmark of professional DevOps tools.

---

## 📝 Quick Reference

| Command | Purpose |
|---------|---------|
| `function_name()` | Define a function |
| `$1, $2, ...` | Access function arguments |
| `local var=value` | Create function-scoped variable |
| `set -e` | Exit on error |
| `set -u` | Error on undefined variables |
| `set -o pipefail` | Fail on pipe errors |
| `$(command)` | Command substitution |
| `${var:-default}` | Use default if var unset |

---

#️⃣ **#90DaysOfDevOps** | **#DevOpsKaJosh** | **#TrainWithShubham** | **#ShellScripting**

---

*Day 18 Complete: Functions, Strict Mode, and Production-Ready Scripts* ✅
