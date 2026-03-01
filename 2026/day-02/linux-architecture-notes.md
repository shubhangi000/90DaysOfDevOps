# Linux Architecture Notes

## 1. Core Components of Linux

### Kernel

- The core of the Linux operating system.
- Acts as a bridge between hardware and software.
- Manages:
  - CPU
  - Memory (RAM)
  - Disk
  - Network
  - Device drivers
- Handles system calls from applications.

## Without the kernel, the system cannot function.

### User Space

- Where user applications run.
- Examples:
  - Shell (bash)
  - Nginx
  - MySQL
  - Docker
- Applications cannot directly access hardware.
- They communicate with the kernel using **system calls**.

---

### Init / systemd

- The first process started by the kernel.
- Has PID = 1.
- Responsible for starting and managing system services.

## Modern Linux uses **systemd** as the init system.

## 2. How Processes Are Created & Managed

### Process Creation

- Every process has a unique **PID (Process ID)**.
- New processes are created using `fork()`.
- The parent process creates a child process.
- The child process may execute a new program using `exec()`.

Example:

- When you run a command in terminal → shell creates a new process.

---

### Process States

| State        | Meaning                                    |
| ------------ | ------------------------------------------ |
| Running (R)  | Currently executing on CPU                 |
| Sleeping (S) | Waiting for an event (I/O, input, etc.)    |
| Stopped (T)  | Paused process                             |
| Zombie (Z)   | Process finished but not cleaned by parent |
| Dead         | Process terminated completely              |

Zombie processes occur when the parent does not collect child exit status.

---

## 3. What systemd Does & Why It Matters

systemd:

- Starts services at boot
- Manages background services (daemons)
- Handles service dependencies
- Restarts failed services automatically
- Maintains system logs (journalctl)

Common systemd commands:

- `systemctl start nginx`
- `systemctl stop nginx`
- `systemctl restart nginx`
- `systemctl status nginx`
- `journalctl -u nginx`

As a DevOps engineer, most production issues involve services failing — systemd helps diagnose and manage them.

---

## 4. 5 Daily Linux Commands for DevOps

- `ps aux` → View running processes
- `top` / `htop` → Monitor CPU & memory usage
- `df -h` → Check disk space
- `free -m` → Check memory usage
- `systemctl status <service>` → Check service health
- `kill -9 <PID>` → Force stop a process

---

## Summary

Linux works in layers:

User Applications → System Calls → Kernel → Hardware

Understanding processes, systemd, and system resources is critical for:

- Troubleshooting production issues
- Managing servers
- Debugging application crashes

This foundation is essential for becoming a strong DevOps engineer.
