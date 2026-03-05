# 🚀 Day 06 – Linux Fundamentals: Read and Write Text Files  
#90DaysOfDevOps

Today I practiced basic file creation, writing, appending, and reading text files in Linux.  
These commands are fundamental for configuration management, logging, scripting, and troubleshooting in real-world environments.

---

## 1️⃣ Create a File

### Command:
```bash
touch notes.txt
```

### Explanation:
- `touch` creates a new empty file if it does not exist.
- If the file already exists, it updates the file’s timestamp.
- Here, `notes.txt` is created as an empty file.

---

## 2️⃣ Write to a File (Overwrite Mode)

### Command:
```bash
echo "Line 1" > notes.txt
```

### Explanation:
- `echo` prints text to standard output.
- `>` redirects the output into a file.
- If the file already contains data, `>` **overwrites** it.
- After this command, `notes.txt` contains:
  ```
  Line 1
  ```

---

## 3️⃣ Append to a File

### Command:
```bash
echo "Line 2" >> notes.txt
```

### Explanation:
- `>>` appends content to the file instead of overwriting.
- The new text is added at the end of the file.
- Now the file contains:
  ```
  Line 1
  Line 2
  ```

---

## 4️⃣ Append Using tee Command

### Command:
```bash
echo "Line 3" | tee -a notes.txt
```

### Explanation:
- `|` (pipe) passes output from one command to another.
- `tee` reads input and writes it to both:
  - Standard output (terminal)
  - A file
- `-a` means **append** (without `-a`, it would overwrite).
- Now the file contains:
  ```
  Line 1
  Line 2
  Line 3
  ```

`tee` is commonly used in scripting and when writing to protected files.

---

## 5️⃣ Display File Content

### Command:
```bash
cat notes.txt
```

### Explanation:
- `cat` displays the entire file content.
- Useful for quickly viewing small files.
- Output:
  ```
  Line 1
  Line 2
  Line 3
  ```

---

## 6️⃣ View First Few Lines

### Command:
```bash
head -n 2 notes.txt
```

### Explanation:
- `head` shows the beginning of a file.
- `-n 2` displays the first 2 lines.
- Output:
  ```
  Line 1
  Line 2
  ```

Helpful when checking logs or large configuration files.

---

## 7️⃣ View Last Few Lines

### Command:
```bash
tail -n 2 notes.txt
```

### Explanation:
- `tail` shows the end of a file.
- `-n 2` displays the last 2 lines.
- Output:
  ```
  Line 2
  Line 3
  ```

Extremely useful for real-time log monitoring (`tail -f filename`).

---

# 🧠 Key Learnings

- `>` overwrites file content.
- `>>` appends content.
- `tee -a` appends while also printing to the terminal.
- `cat`, `head`, and `tail` help quickly inspect file content.
- These commands are essential for DevOps, log analysis, and shell scripting.

---

## 🔥 DevOps Mindset Takeaway

Mastering basic file operations is crucial because real production systems rely heavily on logs, configuration files, and automation scripts.  
Strong fundamentals in text manipulation directly improve troubleshooting speed and operational confidence.
