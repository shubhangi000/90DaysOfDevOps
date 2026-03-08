# Day 09 – Linux User & Group Management Challenge
🚀 #90DaysOfDevOps

## Objective
Today's goal was to practice Linux user and group management through hands-on challenges.

The tasks included:
- Creating users and setting passwords
- Creating groups and assigning users
- Setting up shared directories with group permissions
- Testing access using different users

These are essential system administration and DevOps skills used in managing multi-user Linux environments.

---

# Task 1: Create Users

### Create Users with Home Directories

```bash
sudo useradd -m tokyo
sudo useradd -m berlin
sudo useradd -m professor
```

### Set Passwords

```bash
sudo passwd tokyo
sudo passwd berlin
sudo passwd professor
```

### Verify Users

Check `/etc/passwd`:

```bash
cat /etc/passwd | grep -E "tokyo|berlin|professor"
```

Check home directories:

```bash
ls /home
```

Example Output:

```
tokyo
berlin
professor
ubuntu
```

---

# Task 2: Create Groups

### Create Groups

```bash
sudo groupadd developers
sudo groupadd admins
```

### Verify Groups

```bash
cat /etc/group | grep -E "developers|admins"
```

Example Output:

```
developers:x:1002:
admins:x:1003:
```

---

# Task 3: Assign Users to Groups

### Add Users to Groups

```bash
sudo usermod -aG developers tokyo
sudo usermod -aG developers,admins berlin
sudo usermod -aG admins professor
```

### Verify Group Membership

```bash
groups tokyo
groups berlin
groups professor
```

Example Output:

```
tokyo : tokyo developers
berlin : berlin developers admins
professor : professor admins
```

---

# Task 4: Shared Directory

### Create Directory

```bash
sudo mkdir /opt/dev-project
```

### Set Group Owner

```bash
sudo chown :developers /opt/dev-project
```

### Set Permissions

```bash
sudo chmod 775 /opt/dev-project
```

### Verify Permissions

```bash
ls -ld /opt/dev-project
```

Example Output:

```
drwxrwxr-x 2 root developers 4096 Mar 9 12:00 /opt/dev-project
```

### Test File Creation

Switch user:

```bash
sudo su - tokyo
touch /opt/dev-project/tokyo_file.txt
exit
```

```bash
sudo su - berlin
touch /opt/dev-project/berlin_file.txt
exit
```

Verify files:

```bash
ls -l /opt/dev-project
```

---

# Task 5: Team Workspace

### Create User

```bash
sudo useradd -m nairobi
sudo passwd nairobi
```

### Create Group

```bash
sudo groupadd project-team
```

### Add Users to Group

```bash
sudo usermod -aG project-team nairobi
sudo usermod -aG project-team tokyo
```

### Create Workspace Directory

```bash
sudo mkdir /opt/team-workspace
```

### Set Group Ownership

```bash
sudo chown :project-team /opt/team-workspace
```

### Set Permissions

```bash
sudo chmod 775 /opt/team-workspace
```

### Verify

```bash
ls -ld /opt/team-workspace
```

Example Output:

```
drwxrwxr-x 2 root project-team 4096 Mar 9 12:20 /opt/team-workspace
```

### Test as Nairobi

```bash
sudo su - nairobi
touch /opt/team-workspace/nairobi_file.txt
exit
```

Verify:

```bash
ls -l /opt/team-workspace
```

---

# Users & Groups Created

**Users**
- tokyo
- berlin
- professor
- nairobi

**Groups**
- developers
- admins
- project-team

---

# Group Assignments

| User | Groups |
|-----|------|
| tokyo | developers, project-team |
| berlin | developers, admins |
| professor | admins |
| nairobi | project-team |

---

# Directories Created

| Directory | Group | Permissions |
|------|------|------|
| /opt/dev-project | developers | 775 |
| /opt/team-workspace | project-team | 775 |

---

# Commands Used

```
useradd
passwd
groupadd
usermod
groups
mkdir
chown
chmod
ls
cat
su
```

---

# What I Learned

- How Linux manages multiple users and groups securely.
- How group permissions allow shared collaboration between users.
- How to configure shared directories using group ownership and permissions.
- The importance of using `-m` with `useradd` and `-aG` with `usermod`.
- How to verify permissions and group assignments using Linux commands.

---

✅ **Outcome**

Successfully created users, groups, and shared directories while validating access through different user accounts.  
This exercise helped reinforce real-world Linux user and permission management used in production environments.
