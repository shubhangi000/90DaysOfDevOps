# Day 07 -- Linux File System Hierarchy & Scenario-Based Practice

## Overview

Today I focused on understanding the **Linux File System Hierarchy** and
practicing **real-world troubleshooting scenarios**.

Understanding where system files, logs, and configurations live is
essential for **DevOps engineers**, especially when diagnosing
production issues.

------------------------------------------------------------------------

# Part 1: Linux File System Hierarchy

## 1️⃣ Root Directory `/`

### Purpose

The root directory `/` is the top-level directory of the Linux file
system. Every file and directory starts from here.

### Command

``` bash
ls -l /
```

### When I Would Use This

When exploring the system structure or navigating major directories.

------------------------------------------------------------------------

## 2️⃣ /home

### Purpose

Contains home directories for all regular users.

### Command

``` bash
ls -l /home
```

### When I Would Use This

To manage user files and directories.

------------------------------------------------------------------------

## 3️⃣ /root

### Purpose

Home directory of the root (administrator) user.

### Command

``` bash
ls -l /root
```

### When I Would Use This

When performing administrative tasks.

------------------------------------------------------------------------

## 4️⃣ /etc

### Purpose

Stores system-wide configuration files.

Examples: - /etc/hostname - /etc/passwd - /etc/ssh/

### Command

``` bash
cat /etc/hostname
```

### When I Would Use This

When modifying system configuration or troubleshooting services.

------------------------------------------------------------------------

## 5️⃣ /var/log

### Purpose

Stores system and application logs. This is one of the most important
directories for DevOps engineers.

### Command

``` bash
ls -l /var/log
```

### Find Largest Log Files

``` bash
du -sh /var/log/* 2>/dev/null | sort -h | tail -5
```

### When I Would Use This

When investigating server crashes, service failures, or debugging
issues.

------------------------------------------------------------------------

## 6️⃣ /tmp

### Purpose

Temporary directory used by applications. Files here are usually deleted
after reboot.

### Command

``` bash
ls -l /tmp
```

### When I Would Use This

When applications need temporary storage.

------------------------------------------------------------------------

# Additional Important Directories

## /bin

Contains essential command binaries required for system operation.

Examples: - ls - cp - mv

Command:

``` bash
ls /bin
```

------------------------------------------------------------------------

## /usr/bin

Contains most user-level command binaries.

Examples: - python - git - nano

Command:

``` bash
ls /usr/bin
```

------------------------------------------------------------------------

## /opt

Used for third-party or optional applications.

Example: - custom installed software

Command:

``` bash
ls /opt
```

------------------------------------------------------------------------

# Hands-On Commands Practiced

Check home directory

``` bash
ls -la ~
```

Check hostname configuration

``` bash
cat /etc/hostname
```

Find large log files

``` bash
du -sh /var/log/* 2>/dev/null | sort -h | tail -5
```

------------------------------------------------------------------------

# Part 2: Scenario-Based Practice

## Scenario 1 -- Service Not Starting

A service **myapp** failed after reboot.

### Step 1

``` bash
systemctl status myapp
```

**Why:** Check if the service is running, stopped, or failed.

### Step 2

``` bash
journalctl -u myapp -n 50
```

**Why:** Check recent logs for errors.

### Step 3

``` bash
systemctl is-enabled myapp
```

**Why:** Verify if the service is enabled at boot.

### Step 4

``` bash
systemctl restart myapp
```

**Why:** Restart the service after investigation.

------------------------------------------------------------------------

## Scenario 2 -- High CPU Usage

The server becomes slow.

### Step 1

``` bash
top
```

Shows real-time CPU usage.

### Step 2

``` bash
htop
```

Interactive process viewer.

### Step 3

``` bash
ps aux --sort=-%cpu | head -10
```

Shows top CPU-consuming processes.

------------------------------------------------------------------------

## Scenario 3 -- Finding Service Logs

Developer asks where **docker logs** are located.

### Step 1

``` bash
systemctl status docker
```

Check service status.

### Step 2

``` bash
journalctl -u docker -n 50
```

View last 50 log entries.

### Step 3

``` bash
journalctl -u docker -f
```

Follow logs in real-time.

------------------------------------------------------------------------

## Scenario 4 -- File Permission Issue

Script `/home/user/backup.sh` shows **Permission Denied**.

### Step 1

``` bash
ls -l /home/user/backup.sh
```

Check file permissions.

Example output:

    -rw-r--r--

(No execute permission)

### Step 2

``` bash
chmod +x /home/user/backup.sh
```

Add execute permission.

### Step 3

``` bash
ls -l /home/user/backup.sh
```

Expected output:

    -rwxr-xr-x

### Step 4

``` bash
./backup.sh
```

Run the script.
