
# Linux Commands Cheat Sheet
**Process Management | File System | Networking Troubleshooting**

---

## 1. Process Management

| Command | Usage |
|---------|-------|
| `ps aux` | Show all running processes |
| `ps -ef` | Full-format process listing |
| `top` | Real-time process monitor |
| `htop` | Interactive process viewer |
| `kill PID` | Terminate process by PID |
| `kill -9 PID` | Force kill a process |
| `pkill name` | Kill process by name |
| `bg` | Resume job in background |
| `fg` | Bring job to foreground |
| `jobs` | List background jobs |
| `nice -n 10 command` | Run process with priority |
| `renice 5 PID` | Change priority of running process |
| `uptime` | Show system uptime & load |
| `free -m` | Show memory usage |

---

## 2. File System & File Management

| Command | Usage |
|---------|-------|
| `pwd` | Print current directory |
| `ls -lah` | List files with details |
| `cd directory` | Change directory |
| `mkdir folder` | Create directory |
| `rm -rf folder` | Delete directory recursively |
| `cp source dest` | Copy file |
| `mv old new` | Move or rename file |
| `touch file.txt` | Create empty file |
| `cat file.txt` | Display file contents |
| `nano file.txt` | Edit file |
| `head -n 10 file` | Show first 10 lines |
| `tail -f file.log` | Monitor log file live |
| `find /path -name file` | Search file by name |
| `du -sh folder` | Show folder size |
| `df -h` | Show disk space usage |
| `chmod 755 file` | Change permissions |
| `chown user:group file` | Change ownership |
| `stat file` | Show file details |

---

## 3. Networking & Troubleshooting

| Command | Usage |
|---------|-------|
| `ip addr` | Show IP addresses |
| `ip route` | Show routing table |
| `ping google.com` | Test connectivity |
| `traceroute google.com` | Trace network path |
| `netstat -tulnp` | Show open ports (legacy) |
| `ss -tulnp` | Show listening ports |
| `curl https://example.com` | Test HTTP request |
| `curl -I https://example.com` | Show response headers |
| `wget URL` | Download file |
| `dig domain.com` | DNS lookup |
| `nslookup domain.com` | DNS query |
| `hostname -I` | Show system IP |
| `whoami` | Show logged-in user |
| `history` | Show command history |

---

##  Common Debugging Combos

```bash
ps aux | grep nginx
tail -f /var/log/syslog
df -h | grep /dev
ss -tulnp | grep 80
curl -I https://google.com
