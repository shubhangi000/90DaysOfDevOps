# Day 17 – Shell Scripting: Loops, Arguments & Error Handling

## 🎯 Objective
Improve shell scripting skills by learning loops, command-line arguments, package installation automation, and error handling techniques to build reliable DevOps automation scripts.

---

## 🔹 Task 1: For Loop

### 1.1 Basic For Loop with Items (`for_loop.sh`)

**Script:**
```bash
#!/bin/bash

for fruit in Apple Banana Mango Orange Grapes
do
    echo "$fruit"
done
```

**Output:**
```
Apple
Banana
Mango
Orange
Grapes
```

**Explanation:** Iterates through a list of fruit names and prints each one. The `for` loop assigns each item sequentially to the `fruit` variable.

---

### 1.2 For Loop with Range (`count.sh`)

**Script:**
```bash
#!/bin/bash

for i in {1..10}
do
    echo "$i"
done
```

**Output:**
```
1
2
3
4
5
6
7
8
9
10
```

**Explanation:** Uses the `{1..10}` range expansion to iterate from 1 to 10. Useful for counting operations and numbered iterations.

---

## 🔹 Task 2: While Loop

### 2.1 Countdown with While Loop (`countdown.sh`)

**Script:**
```bash
#!/bin/bash

echo "Enter a number:"
read num

while [ $num -ge 0 ]
do
    echo "$num"
    num=$((num - 1))
done

echo "Done!"
```

**Output (with input 5):**
```
Enter a number:
5
5
4
3
2
1
0
Done!
```

**Explanation:** 
- Prompts user for input using `read num`
- While loop continues as long as `$num -ge 0` (greater than or equal to 0)
- Decrements `num` by 1 in each iteration using `$((num - 1))`
- Prints "Done!" when loop exits
- Demonstrates condition-based looping

---

## 🔹 Task 3: Command-Line Arguments

### 3.1 Handling Single Argument (`greet.sh`)

**Script:**
```bash
#!/bin/bash

if [ -z "$1" ]
then
    echo "Usage: ./greet.sh <name>"
else
    echo "Hello, $1!"
fi
```

**Output (with argument):**
```
$ ./greet.sh Shubhangi
Hello, Shubhangi!
```

**Output (without argument):**
```
$ ./greet.sh
Usage: ./greet.sh <name>
```

**Explanation:**
- `$1` refers to the first command-line argument
- `[ -z "$1" ]` tests if the first argument is empty
- Provides helpful usage message when argument is missing
- Essential for building user-friendly scripts

---

### 3.2 Multiple Arguments Demo (`args_demo.sh`)

**Script:**
```bash
#!/bin/bash

echo "Script name: $0"
echo "Total arguments: $#"
echo "All arguments: $@"
```

**Output:**
```
$ ./args_demo.sh DevOps Linux Shell
Script name: ./args_demo.sh
Total arguments: 3
All arguments: DevOps Linux Shell
```

**Explanation:**
- `$0` = script name or path
- `$#` = total number of arguments passed
- `$@` = all arguments as separate items
- `$1, $2, $3...` = individual arguments (1st, 2nd, 3rd, etc.)

| Variable | Meaning | Example |
|----------|---------|---------|
| `$0` | Script name | `./args_demo.sh` |
| `$1, $2, $3...` | Individual arguments | `DevOps`, `Linux`, `Shell` |
| `$#` | Total argument count | `3` |
| `$@` | All arguments | `DevOps Linux Shell` |
| `$*` | All arguments as single string | `DevOps Linux Shell` |

---

## 🔹 Task 4: Install Packages via Script

### 4.1 Automated Package Installation (`install_packages.sh`)

**Script:**
```bash
#!/bin/bash

if [ "$EUID" -ne 0 ]
then
    echo "Please run as root"
    exit 1
fi

packages=("nginx" "curl" "wget")

for package in "${packages[@]}"
do
    if dpkg -s "$package" &> /dev/null
    then
        echo "$package is already installed"
    else
        echo "Installing $package..."
        apt update && apt install -y "$package"
    fi
done
```

**Output (example):**
```
curl is already installed
wget is already installed
Installing nginx...
[apt install process output...]
```

**Key Features:**

1. **Root User Check:**
   - `[ "$EUID" -ne 0 ]` checks if user is not root
   - `exit 1` exits with error status if not root

2. **Array Declaration:**
   - `packages=("nginx" "curl" "wget")` creates an array
   - `"${packages[@]}"` iterates through all array elements

3. **Package Detection:**
   - `dpkg -s "$package"` checks if package is installed
   - `&> /dev/null` suppresses output (redirects both stdout and stderr)
   - Prevents duplicate installations

4. **Installation:**
   - `apt update` refreshes package lists
   - `apt install -y` installs without prompting
   - `&&` operator ensures install only runs if update succeeds

**Benefits:**
- Prevents unnecessary reinstallation
- Requires proper permissions
- Automates system setup
- Works on Debian/Ubuntu systems

---

## 🔹 Task 5: Error Handling

### 5.1 Safe Script with Error Handling (`safe_script.sh`)

**Script:**
```bash
#!/bin/bash

set -e

mkdir /tmp/devops-test || echo "Directory already exists"

cd /tmp/devops-test || echo "Failed to enter directory"

touch sample.txt || echo "File creation failed"

echo "Script completed successfully"
```

**Output:**
```
Directory already exists
Script completed successfully
```

**Error Handling Techniques:**

1. **`set -e` (Exit on Error):**
   - Exits script immediately if any command fails (non-zero exit status)
   - Prevents execution of subsequent commands after failure
   - Makes scripts more robust and prevents cascading failures

2. **`||` Operator (OR):**
   - Provides fallback action if command fails
   - `command || alternative` runs alternative only if command fails
   - Example: `mkdir dir || echo "Failed to create directory"`

3. **`&&` Operator (AND):**
   - Runs next command only if previous succeeds
   - `command1 && command2` runs command2 only if command1 succeeds
   - Example: `apt update && apt install -y package`

4. **Redirecting Output:**
   - `&> /dev/null` redirects both stdout and stderr to null
   - Silences command output for cleaner logs
   - Useful when you only care about success/failure

**Error Handling Operators:**

| Operator | Usage | Meaning |
|----------|-------|---------|
| `&&` | `cmd1 && cmd2` | Execute cmd2 only if cmd1 succeeds |
| `\|\|` | `cmd1 \|\| cmd2` | Execute cmd2 only if cmd1 fails |
| `set -e` | At script start | Exit immediately on any error |
| `set -u` | At script start | Exit if undefined variable is used |
| `set -o pipefail` | At script start | Exit if pipe command fails |
| `&> /dev/null` | After command | Suppress all output |

---

## 📘 What I Learned Today

### Core Concepts

1. **For Loops:**
   - Iterate over lists: `for item in list`
   - Iterate over ranges: `for i in {1..10}`
   - Process arrays: `for element in "${array[@]}"`

2. **While Loops:**
   - Condition-based iteration: `while [ condition ]`
   - Useful for reading input and counter operations
   - Must update loop variable to avoid infinite loops

3. **Command-Line Arguments:**
   - `$0` = script name
   - `$1, $2, $3...` = individual arguments
   - `$#` = total number of arguments
   - `$@` = all arguments as array
   - Always validate arguments before using them

4. **Arrays in Bash:**
   - Declare: `array=("item1" "item2" "item3")`
   - Iterate: `for item in "${array[@]}"`
   - Access: `${array[0]}` for first element

5. **Package Management Automation:**
   - Check if installed: `dpkg -s package_name`
   - Prevents duplicate installations
   - Automates system setup and maintenance
   - Requires root privileges for apt operations

6. **Error Handling Best Practices:**
   - Use `set -e` to exit on any error
   - Combine with `||` for graceful fallbacks
   - Use `&&` to chain dependent commands
   - Suppress unnecessary output with `&> /dev/null`
   - Always validate conditions before executing critical operations

### Practical Skills

✅ Creating robust, reusable shell scripts  
✅ Automating repetitive system tasks  
✅ Handling errors gracefully  
✅ Writing defensive scripts that validate input  
✅ Managing packages programmatically  
✅ Using conditional logic in automation  
✅ Working with arrays and complex data structures  

### DevOps Applications

- **Server Setup:** Automate package installation and configuration
- **System Monitoring:** Create loops to check system status
- **Backup Scripts:** Iterate through files and directories
- **Deployment Automation:** Chain commands with error handling
- **Log Processing:** Loop through logs and filter data
- **Health Checks:** Validate services and permissions

---

## 🚀 Key Takeaways

Shell scripting is a foundational DevOps skill that enables:

1. **Automation:** Eliminate manual, repetitive tasks
2. **Reliability:** Error handling ensures predictable execution
3. **Efficiency:** Process multiple items with loops
4. **Flexibility:** Accept dynamic input via arguments
5. **Scalability:** Easy to extend and maintain
6. **Integration:** Connect tools and services seamlessly

These fundamental concepts—loops, arguments, and error handling—are the building blocks for more advanced DevOps scripts including deployment automation, infrastructure provisioning, and system administration tasks.

---

## 📚 Quick Reference

### Loop Syntax
```bash
# For loop
for var in list; do
    # commands
done

# While loop
while [ condition ]; do
    # commands
done

# Until loop (opposite of while)
until [ condition ]; do
    # commands
done
```

### Argument Handling
```bash
#!/bin/bash

# Validate argument count
if [ $# -lt 1 ]; then
    echo "Usage: $0 <argument>"
    exit 1
fi

# Use arguments
name=$1
count=$2
all_args=$@
```

### Error Handling
```bash
#!/bin/bash
set -e  # Exit on error

# Conditional execution
command && echo "Success" || echo "Failed"

# With fallback
mkdir dir || echo "Directory exists"

# Suppress output
cmd &> /dev/null

# Check exit status
if [ $? -eq 0 ]; then
    echo "Command succeeded"
fi
```

---

**Completed on:** Day 17  
**Topics Covered:** For Loops, While Loops, Command-Line Arguments, Package Installation, Error Handling  
**Skills Gained:** Shell Scripting, DevOps Automation, System Administration
