# Day 11 Challenge – File Ownership (chown & chgrp)

## Overview
Master file and directory ownership in Linux by understanding and practicing `chown` (change owner) and `chgrp` (change group) commands.

---

## Task 1: Understanding Ownership (10 minutes)

### File Permission Format
```
-rw-r--r-- 1 owner group size date filename
```

**Breaking Down the Components:**
- First character: File type (- = regular file, d = directory)
- Next 9 characters: Permission bits (rwxrwxrwx = user, group, others)
- Number: Hard link count
- **Owner**: User who owns the file
- **Group**: Group that owns the file
- Size, Date, Filename follow

### View Current Ownership
```bash
ls -l
```

### View All Usernames on System
```bash
cut -d: -f1 /etc/passwd
```

**Explanation:**
- `cut` - Extract columns from text
- `-d:` - Use colon as delimiter (passwd file uses colons)
- `-f1` - Select field 1 (username column)

### What's the Difference?

| Aspect | Owner | Group |
|--------|-------|-------|
| **Definition** | Individual user who owns the file | Set of users with shared access |
| **Permissions** | Middle 3 rwx bits apply | Second 3 rwx bits apply |
| **Use Case** | Restrict to single user | Allow team collaboration |
| **Example** | `ubuntu` owns a file | `developers` group can edit |

---

## Task 2: Basic chown Operations (20 minutes)

### Step 1: Create a Test File
```bash
touch devops-file.txt
```

### Step 2: Check Current Owner
```bash
ls -l devops-file.txt
```
**Expected Output:**
```
-rw-r--r-- 1 ubuntu ubuntu 0 Mar 13 10:00 devops-file.txt
```

### Step 3: Create Required Users (if needed)
```bash
# Create tokyo user
sudo useradd -m tokyo

# Create berlin user
sudo useradd -m berlin

# Verify users exist
cut -d: -f1 /etc/passwd | grep -E 'tokyo|berlin'
```

### Step 4: Change Owner to tokyo
```bash
sudo chown tokyo devops-file.txt
ls -l devops-file.txt
```
**Expected Output:**
```
-rw-r--r-- 1 tokyo ubuntu 0 Mar 13 10:00 devops-file.txt
```

### Step 5: Change Owner to berlin
```bash
sudo chown berlin devops-file.txt
ls -l devops-file.txt
```
**Expected Output:**
```
-rw-r--r-- 1 berlin ubuntu 0 Mar 13 10:00 devops-file.txt
```

### Summary of Changes
```
devops-file.txt: ubuntu → tokyo → berlin
Group remained: ubuntu (unchanged)
```

---

## Task 3: Basic chgrp Operations (15 minutes)

### Step 1: Create a Test File
```bash
touch team-notes.txt
ls -l team-notes.txt
```

### Step 2: Create Custom Group
```bash
sudo groupadd heist-team
```

### Step 3: Verify Group Creation
```bash
# View group file
cat /etc/group | grep heist-team

# Or list groups for current user
groups
```

### Step 4: Change File Group
```bash
sudo chgrp heist-team team-notes.txt
ls -l team-notes.txt
```
**Expected Output:**
```
-rw-r--r-- 1 ubuntu heist-team 0 Mar 13 10:00 team-notes.txt
```

### Summary of Changes
```
team-notes.txt: ubuntu:ubuntu → ubuntu:heist-team
Owner remained: ubuntu (unchanged)
```

---

## Task 4: Combined Owner & Group Change (15 minutes)

### Understanding the Syntax
```bash
sudo chown owner:group filename
```

### Step 1: Create Config File
```bash
touch project-config.yaml
```

### Step 2: Create Required User and Group
```bash
# Create professor user
sudo useradd -m professor

# Group heist-team already exists from Task 3
```

### Step 3: Change Both Owner and Group (Single Command)
```bash
sudo chown professor:heist-team project-config.yaml
ls -l project-config.yaml
```
**Expected Output:**
```
-rw-r--r-- 1 professor heist-team 0 Mar 13 10:00 project-config.yaml
```

### Step 4: Create Directory and Change Ownership
```bash
mkdir app-logs

# Create berlin user if needed
sudo useradd -m berlin

# Change both owner and group
sudo chown berlin:heist-team app-logs
ls -ld app-logs
```
**Expected Output:**
```
drwxr-xr-x 2 berlin heist-team 4096 Mar 13 10:00 app-logs
```

### Summary of Changes
```
project-config.yaml: ubuntu:ubuntu → professor:heist-team
app-logs/: ubuntu:ubuntu → berlin:heist-team
```

---

## Task 5: Recursive Ownership (20 minutes)

### Step 1: Create Directory Structure
```bash
mkdir -p heist-project/vault
mkdir -p heist-project/plans
touch heist-project/vault/gold.txt
touch heist-project/plans/strategy.conf
touch heist-project/README.txt
```

### Step 2: Verify Initial Structure
```bash
ls -lR heist-project/
```
**Expected Output:**
```
heist-project/:
total 4
drwxr-xr-x 2 ubuntu ubuntu 4096 Mar 13 10:00 plans
drwxr-xr-x 2 ubuntu ubuntu 4096 Mar 13 10:00 vault
-rw-r--r-- 1 ubuntu ubuntu    0 Mar 13 10:00 README.txt

heist-project/plans:
total 0
-rw-r--r-- 1 ubuntu ubuntu 0 Mar 13 10:00 strategy.conf

heist-project/vault:
total 0
-rw-r--r-- 1 ubuntu ubuntu 0 Mar 13 10:00 gold.txt
```

### Step 3: Create planners Group
```bash
sudo groupadd planners
```

### Step 4: Recursive Ownership Change
```bash
# Change entire directory tree: owner to professor, group to planners
sudo chown -R professor:planners heist-project/
```

**Flags Explained:**
- `-R` - Recursive (applies to all files and subdirectories)
- `professor` - New owner for all items
- `planners` - New group for all items

### Step 5: Verify Recursive Changes
```bash
ls -lR heist-project/
```
**Expected Output:**
```
heist-project/:
total 4
drwxr-xr-x 2 professor planners 4096 Mar 13 10:00 plans
drwxr-xr-x 2 professor planners 4096 Mar 13 10:00 vault
-rw-r--r-- 1 professor planners    0 Mar 13 10:00 README.txt

heist-project/plans:
total 0
-rw-r--r-- 1 professor planners 0 Mar 13 10:00 strategy.conf

heist-project/vault:
total 0
-rw-r--r-- 1 professor planners 0 Mar 13 10:00 gold.txt
```

### Summary of Changes
```
Before: all owned by ubuntu:ubuntu
After:  all owned by professor:planners (entire tree changed with -R)
```

---

## Task 6: Practice Challenge (20 minutes)

### Step 1: Create Users
```bash
# Create all required users
sudo useradd -m tokyo
sudo useradd -m berlin
sudo useradd -m nairobi

# Verify creation
cut -d: -f1 /etc/passwd | grep -E 'tokyo|berlin|nairobi'
```

### Step 2: Create Groups
```bash
sudo groupadd vault-team
sudo groupadd tech-team

# Verify groups
cat /etc/group | grep -E 'vault-team|tech-team'
```

### Step 3: Create Directory Structure
```bash
mkdir bank-heist
touch bank-heist/access-codes.txt
touch bank-heist/blueprints.pdf
touch bank-heist/escape-plan.txt

# Verify structure
ls -l bank-heist/
```

### Step 4: Set Specific Ownership for Each File
```bash
# access-codes.txt → owner: tokyo, group: vault-team
sudo chown tokyo:vault-team bank-heist/access-codes.txt

# blueprints.pdf → owner: berlin, group: tech-team
sudo chown berlin:tech-team bank-heist/blueprints.pdf

# escape-plan.txt → owner: nairobi, group: vault-team
sudo chown nairobi:vault-team bank-heist/escape-plan.txt
```

### Step 5: Verify All Changes
```bash
ls -l bank-heist/
```
**Expected Output:**
```
total 0
-rw-r--r-- 1 tokyo    vault-team 0 Mar 13 10:00 access-codes.txt
-rw-r--r-- 1 berlin   tech-team  0 Mar 13 10:00 blueprints.pdf
-rw-r--r-- 1 nairobi  vault-team 0 Mar 13 10:00 escape-plan.txt
```

### Summary of Changes
```
access-codes.txt:  ubuntu:ubuntu → tokyo:vault-team
blueprints.pdf:    ubuntu:ubuntu → berlin:tech-team
escape-plan.txt:   ubuntu:ubuntu → nairobi:vault-team
```

---

## Key Commands Reference

### View Ownership
```bash
# List files with ownership details
ls -l filename

# List directory contents with ownership
ls -l directory/

# Recursive directory listing
ls -lR directory/

# List files owned by specific user
ls -l | grep username
```

### Change Owner (chown)
```bash
# Change owner only
sudo chown newowner filename

# Change owner recursively
sudo chown -R newowner directory/

# Change both owner and group
sudo chown owner:group filename

# Change group only (using chown)
sudo chown :groupname filename
```

### Change Group (chgrp)
```bash
# Change group only
sudo chgrp newgroup filename

# Change group recursively
sudo chgrp -R newgroup directory/
```

### User & Group Management
```bash
# Create user
sudo useradd -m username

# Create group
sudo groupadd groupname

# List all users
cut -d: -f1 /etc/passwd

# List all groups
cat /etc/group

# List user's groups
groups username
```

---

## Important Notes

### Requirements for chown/chgrp
- ✅ Most operations require `sudo` (unless changing ownership of your own file)
- ✅ **User must exist** before using in `chown`
- ✅ **Group must exist** before using in `chgrp` or `chown`
- ✅ Use `-R` flag for recursive directory changes
- ✅ **Always verify** with `ls -l` after changes

### Common Mistakes
```bash
# ❌ WRONG - User doesn't exist
sudo chown nonexistent-user file.txt

# ✅ CORRECT - Create user first
sudo useradd -m newuser
sudo chown newuser file.txt

# ❌ WRONG - Group doesn't exist
sudo chown owner:badgroup file.txt

# ✅ CORRECT - Create group first
sudo groupadd newgroup
sudo chown owner:newgroup file.txt

# ❌ WRONG - Forgot -R for directories
sudo chown -R newowner directory/

# ✅ CORRECT - Forgot flag means only parent directory changes
sudo chown -R newowner directory/  # This is correct!
```

---

## Files & Directories Created

### Task 2
- `devops-file.txt` - Owner changed: ubuntu → tokyo → berlin

### Task 3
- `team-notes.txt` - Group changed: ubuntu → heist-team

### Task 4
- `project-config.yaml` - Both changed: ubuntu:ubuntu → professor:heist-team
- `app-logs/` - Both changed: ubuntu:ubuntu → berlin:heist-team

### Task 5
- `heist-project/` (directory structure)
  - `heist-project/vault/gold.txt`
  - `heist-project/plans/strategy.conf`
  - `heist-project/README.txt`
  - All changed to: professor:planners

### Task 6
- `bank-heist/` (directory structure)
  - `bank-heist/access-codes.txt` → tokyo:vault-team
  - `bank-heist/blueprints.pdf` → berlin:tech-team
  - `bank-heist/escape-plan.txt` → nairobi:vault-team

### Users Created
- `tokyo`
- `berlin`
- `nairobi`
- `professor`

### Groups Created
- `heist-team`
- `planners`
- `vault-team`
- `tech-team`

---

## Summary of Ownership Changes

| File/Directory | Initial Ownership | Final Ownership | Command Used |
|---|---|---|---|
| devops-file.txt | ubuntu:ubuntu | berlin:ubuntu | `sudo chown berlin` |
| team-notes.txt | ubuntu:ubuntu | ubuntu:heist-team | `sudo chgrp heist-team` |
| project-config.yaml | ubuntu:ubuntu | professor:heist-team | `sudo chown professor:heist-team` |
| app-logs/ | ubuntu:ubuntu | berlin:heist-team | `sudo chown berlin:heist-team` |
| heist-project/ (recursive) | ubuntu:ubuntu | professor:planners | `sudo chown -R professor:planners` |
| bank-heist/access-codes.txt | ubuntu:ubuntu | tokyo:vault-team | `sudo chown tokyo:vault-team` |
| bank-heist/blueprints.pdf | ubuntu:ubuntu | berlin:tech-team | `sudo chown berlin:tech-team` |
| bank-heist/escape-plan.txt | ubuntu:ubuntu | nairobi:vault-team | `sudo chown nairobi:vault-team` |

---

## What I Learned

### 1. **File Ownership is Two-Part System**
File ownership consists of two independent components:
- **Owner (User)**: Individual user who owns the file, controls permissions via first rwx bits
- **Group**: Set of users who share group permissions, controls permissions via second rwx bits
- These can be changed independently or together
- Ownership affects permission enforcement and file access control

### 2. **The -R Flag is Essential for Directories**
- Without `-R`, `chown`/`chgrp` only changes the parent directory
- With `-R`, changes apply to the directory AND all contents recursively
- This is critical when managing entire project directories
- Always verify with `ls -lR` after recursive changes to ensure all files changed

### 3. **Prerequisites: Users and Groups Must Exist First**
- Cannot assign ownership to non-existent users (error: "chown: invalid user")
- Cannot assign group to non-existent groups (error: "chown: invalid group")
- Must create with `useradd -m` and `groupadd` before using in chown/chgrp
- The `-m` flag creates home directory for new users
- Always verify with `cut -d: -f1 /etc/passwd` and `cat /etc/group` before assigning

---

## Key Syntax Patterns

```bash
# Changing owner only
sudo chown newowner file.txt

# Changing group only (method 1)
sudo chgrp newgroup file.txt

# Changing group only (method 2)
sudo chown :newgroup file.txt

# Changing both (use colon separator)
sudo chown owner:group file.txt

# Recursive changes (always use -R for directories)
sudo chown -R owner:group directory/

# View usernames
cut -d: -f1 /etc/passwd

# Verify changes
ls -l file.txt
ls -lR directory/
```

---

## Challenge Completion Checklist

- ✅ Task 1: Understood ownership difference between user and group
- ✅ Task 2: Changed file owner with `chown`
- ✅ Task 3: Changed file group with `chgrp`
- ✅ Task 4: Changed both owner and group simultaneously
- ✅ Task 5: Applied recursive ownership changes with `-R` flag
- ✅ Task 6: Practiced with multiple users, groups, and files
- ✅ Verified all changes with `ls -l` and `ls -lR`
- ✅ Created all required users and groups
- ✅ Documented all changes and commands used

---

## Next Steps

- Practice changing ownership on your own files
- Experiment with permission bits combined with ownership
- Learn about special permissions (setuid, setgid, sticky bit)
- Explore user and group management in more depth
- Connect ownership concepts to access control and security
