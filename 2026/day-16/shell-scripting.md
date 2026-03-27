# 📄 Shell Scripting Basics - Day 16

## 🔹 What is Shell Scripting?

Shell scripting is writing a sequence of commands in a file to automate tasks in a Unix/Linux environment. It allows you to combine multiple commands into a single executable file, reducing manual work and increasing efficiency.

---

## 🔹 Shebang (#!)

```bash
#!/bin/bash
```

### Why it matters:
- **Tells the system which interpreter to use** - The shebang line must be the first line of your script
- **Ensures script runs with correct shell** - Different shells (bash, sh, zsh) have different syntax
- **Makes script portable and predictable** - Guarantees consistent execution across systems

---

## 🔹 Basic Commands

### echo (Print Output)

```bash
echo "Hello World"
```

Output:
```
Hello World
```

### Variables

```bash
NAME="Shubhangi"
echo $NAME
```

Output:
```
Shubhangi
```

⚠️ **Rules:**
- No spaces around `=` (e.g., `NAME="value"` ✓, `NAME = "value"` ✗)
- Use `$` to access the value (e.g., `$NAME`)
- Variable names are case-sensitive

---

## 🔹 Quotes Difference

```bash
echo 'Hello $NAME'   # No expansion
echo "Hello $NAME"   # Expands variable
```

### Output:
```
Hello $NAME
Hello Shubhangi
```

**Key Distinction:**
- 👉 **Single quotes** → Treat content as literal (no variable expansion)
- 👉 **Double quotes** → Allow variable expansion and command substitution

---

## 🔹 User Input

```bash
read -p "Enter your name: " NAME
echo "Hello $NAME"
```

**Command breakdown:**
- `read` - Waits for user input
- `-p` - Displays a prompt before reading
- `NAME` - Variable to store the input

---

## 🔹 If-Else Conditions

### Syntax:

```bash
if [ condition ]; then
    # code to execute if condition is true
elif [ condition ]; then
    # code to execute if second condition is true
else
    # code to execute if no condition is true
fi
```

### Example:

```bash
#!/bin/bash

NUM=10

if [ $NUM -gt 0 ]; then
    echo "Positive"
elif [ $NUM -lt 0 ]; then
    echo "Negative"
else
    echo "Zero"
fi
```

Output:
```
Positive
```

---

## 🔹 Comparison Operators

| Operator | Meaning |
|----------|---------|
| `-eq` | Equal to |
| `-ne` | Not equal to |
| `-gt` | Greater than |
| `-lt` | Less than |
| `-ge` | Greater than or equal to |
| `-le` | Less than or equal to |

### Example:

```bash
AGE=25

if [ $AGE -ge 18 ]; then
    echo "You are an adult"
else
    echo "You are a minor"
fi
```

---

## 🔹 File Conditions

```bash
if [ -f filename ]; then
    echo "File exists"
else
    echo "File not found"
fi
```

### Common File Checks:

| Operator | Check |
|----------|-------|
| `-f` | File exists |
| `-d` | Directory exists |
| `-e` | File or directory exists |
| `-r` | File is readable |
| `-w` | File is writable |
| `-x` | File is executable |
| `-s` | File exists and has size > 0 |

### Example:

```bash
#!/bin/bash

FILE="/etc/passwd"

if [ -f "$FILE" ]; then
    echo "File exists and is readable"
else
    echo "File not found"
fi
```

---

## 🔹 Making Script Executable

```bash
chmod +x script.sh
./script.sh
```

**Command breakdown:**
- `chmod +x` - Adds execute permission to the script
- `./script.sh` - Runs the script from current directory

---

## 🔹 Running a Script

### Method 1: Using bash directly

```bash
bash script.sh
```

### Method 2: Make executable and run

```bash
chmod +x script.sh
./script.sh
```

---

## 🔹 Example Script: Greeting

```bash
#!/bin/bash

read -p "Enter your name: " NAME
read -p "Enter your role: " ROLE

echo "Hello $NAME, you are a $ROLE"
```

### Usage:
```
$ chmod +x greeting.sh
$ ./greeting.sh
Enter your name: Shubhangi
Enter your role: Developer
Hello Shubhangi, you are a Developer
```

---

## 🔹 Mini Automation Example: Service Status Checker

```bash
#!/bin/bash

SERVICE="nginx"

STATUS=$(systemctl is-active $SERVICE)

if [ "$STATUS" == "active" ]; then
    echo "$SERVICE is running"
else
    echo "$SERVICE is not running"
fi
```

### Key Concepts:
- `$(command)` - Command substitution (captures output into a variable)
- `systemctl is-active` - Checks if a service is running
- String comparison using `==`

### Output:
```
nginx is running
```

---

## 🔹 Additional Useful Constructs

### For Loop

```bash
#!/bin/bash

for i in 1 2 3 4 5; do
    echo "Number: $i"
done
```

### While Loop

```bash
#!/bin/bash

count=1
while [ $count -le 5 ]; do
    echo "Count: $count"
    ((count++))
done
```

### Case Statement

```bash
#!/bin/bash

read -p "Enter a day: " DAY

case $DAY in
    Monday)
        echo "Start of the week"
        ;;
    Friday)
        echo "Almost weekend!"
        ;;
    *)
        echo "Regular day"
        ;;
esac
```

---

## 🔹 Key Learnings

- ✅ **Shebang defines interpreter** - Always start with `#!/bin/bash`
- ✅ **Variables and quotes behave differently** - Single vs double quotes matter
- ✅ **Scripts can automate repetitive tasks** - Save time and reduce errors
- ✅ **Conditions help in decision-making** - Use if-else and case statements
- ✅ **File operations are powerful** - Check file existence, permissions, and size
- ✅ **Command substitution extends functionality** - Use `$(command)` to capture output

---

## 🚀 Conclusion

Shell scripting is a powerful tool for:
- 🔧 **Automation** - Automate repetitive daily tasks
- 👨‍💼 **System Administration** - Manage systems efficiently
- 🔄 **DevOps Workflows** - Create CI/CD pipelines
- 📊 **Data Processing** - Process logs and files in bulk

**Even small scripts can save significant time and effort!**

---

## 📝 Quick Reference Cheat Sheet

```bash
# Variables
NAME="value"                    # Define variable
echo $NAME                      # Access variable
read -p "Prompt: " VAR         # Get user input

# Conditions
if [ $NUM -gt 5 ]; then        # If NUM > 5
elif [ $NUM -eq 5 ]; then      # Else if NUM = 5
else                            # Else
fi                              # End if

# File Checks
[ -f FILE ]                    # File exists?
[ -d DIR ]                     # Directory exists?
[ -e FILE ]                    # File/Dir exists?

# Loops
for i in 1 2 3; do             # For loop
done

while [ condition ]; do        # While loop
done

# Command Substitution
OUTPUT=$(command)              # Run command, store output

# Execute
chmod +x script.sh             # Make executable
./script.sh                    # Run script
bash script.sh                 # Run with bash
```

---

**Happy Scripting! 🎉**
