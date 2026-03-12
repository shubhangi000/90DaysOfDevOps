# Day 10 – File Permissions & File Operations Challenge

## Overview
Today's goal was to understand how Linux handles file permissions and perform common file operations such as creating, reading, and modifying files.

---

## Task 1: Create Files

### Create empty file
```bash
touch devops.txt
```

### Create file with content
```bash
echo "Linux permissions are important for security." > notes.txt
```

### Create script using vim
```bash
vim script.sh
```

Add the following content:
```bash
echo "Hello DevOps"
```

### Verify permissions
```bash
ls -l
```

**Example Output:**
```
-rw-r--r-- 1 user user   0 Mar 10 10:00 devops.txt
-rw-r--r-- 1 user user  45 Mar 10 10:02 notes.txt
-rw-r--r-- 1 user user  20 Mar 10 10:03 script.sh
```

---

## Task 2: Read Files

### Read notes.txt
```bash
cat notes.txt
```

### View script.sh in read-only mode
```bash
vim -R script.sh
```

### Display first 5 lines of /etc/passwd
```bash
head -n 5 /etc/passwd
```

### Display last 5 lines of /etc/passwd
```bash
tail -n 5 /etc/passwd
```

---

## Task 3: Understand Permissions

### Permission Format
```
rwxrwxrwx
```

### Permission Breakdown

| Permission | Value |
|------------|-------|
| r (Read)   | 4     |
| w (Write)  | 2     |
| x (Execute)| 1     |

### Check Permissions
```bash
ls -l devops.txt notes.txt script.sh
```

**Example Output:**
```
-rw-r--r-- devops.txt
-rw-r--r-- notes.txt
-rw-r--r-- script.sh
```

### Permission Breakdown

| Category | Permissions |
|----------|-------------|
| Owner    | Read, Write |
| Group    | Read        |
| Others   | Read        |

**Note:** `script.sh` does not have execute permission initially.

---

## Task 4: Modify Permissions

### Make script executable
```bash
chmod +x script.sh
```

### Run script
```bash
./script.sh
```

**Output:**
```
Hello DevOps
```

### Make devops.txt read-only
```bash
chmod a-w devops.txt
```

### Check
```bash
ls -l devops.txt
```

**Example:**
```
-r--r--r-- devops.txt
```

### Set notes.txt permission to 640
```bash
chmod 640 notes.txt
```

**Meaning:**
- Owner → read + write (6)
- Group → read (4)
- Others → none (0)

### Create directory with 755 permissions
```bash
mkdir project
chmod 755 project
```

### Verify
```bash
ls -ld project
```

---

## Task 5: Test Permissions

### Try writing to read-only file
```bash
echo "test" >> devops.txt
```

**Error:**
```
Permission denied
```

### Try executing file without execute permission
```bash
chmod -x script.sh
./script.sh
```

**Error:**
```
Permission denied
```

---

## Commands Summary

| Command | Purpose                           |
|---------|-----------------------------------|
| `touch` | Create empty file                 |
| `echo`  | Print text or write to file       |
| `vim`   | Edit files in terminal            |
| `ls -l` | List files with detailed info     |
| `cat`   | Display file contents             |
| `head`  | Show first lines of file          |
| `tail`  | Show last lines of file           |
| `chmod` | Change file/directory permissions |
| `mkdir` | Create directory                  |

---

## Key Takeaways

- **Linux file permissions** control who can read, write, and execute files
- **Three permission levels:**
  - **Owner:** The user who created the file
  - **Group:** Users belonging to the file's group
  - **Others:** All other users on the system
- **chmod command** is used to modify file and directory permissions
- **Permission notation:**
  - Symbolic: `chmod +x file` (add execute)
  - Octal: `chmod 755 file` (rwxr-xr-x)
- **Understanding permissions** is critical for security and proper system administration
