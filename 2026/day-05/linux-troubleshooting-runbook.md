# Linux Troubleshooting Runbook – Day 05

## Drill: CPU, Memory, Disk, Network & Logs

---

## 🎯 Target Service / Process

**Service:** nginx
**Environment:** AWS EC2 (Ubuntu)

---

## 🖥 Environment Basics

### 1. OS Information

```bash
uname -a
```

Observation: Kernel version and architecture confirmed (x86_64).

```bash
cat /etc/os-release
```

Observation: Running Ubuntu 22.04 LTS.

---

## 📂 Filesystem Sanity Check

```bash
mkdir /tmp/runbook-demo
cp /etc/hosts /tmp/runbook-demo/hosts-copy
ls -l /tmp/runbook-demo
```

Observation: File successfully copied. Permissions and ownership look correct. Filesystem responsive.

---

## 📊 Snapshot: CPU & Memory

### 2. System Resource Usage

```bash
top
```

Observation: CPU usage under 10%, memory stable, no abnormal spikes.

### 3. Nginx Process Usage

```bash
ps -o pid,pcpu,pmem,comm -C nginx
```

Observation: Master and worker processes running. CPU and memory usage minimal.

### 4. Memory Overview

```bash
free -h
```

Observation: Sufficient available memory. No swap pressure detected.

---

## 💽 Snapshot: Disk & IO

### 5. Disk Usage

```bash
df -h
```

Observation: Root partition below 60% usage. No disk pressure.

### 6. Log Directory Size

```bash
du -sh /var/log
```

Observation: Log directory size reasonable. No unexpected growth.

---

## 🌐 Snapshot: Network

### 7. Listening Ports

```bash
ss -tulpn | grep nginx
```

Observation: Nginx listening on port 80 (HTTP). Port binding correct.

### 8. HTTP Response Check

```bash
curl -I http://localhost
```

Observation: HTTP/1.1 200 OK returned. Service responding correctly.

---

## 📜 Logs Reviewed

### 9. Systemd Logs

```bash
journalctl -u nginx -n 50 --no-pager
```

Observation: No recent errors. Service started successfully.

### 10. Nginx Error Log

```bash
tail -n 50 /var/log/nginx/error.log
```

Observation: No critical errors found.

---

## 🔎 Quick Findings

* Nginx service is active and running.
* CPU and memory usage within normal limits.
* No disk saturation.
* Network port 80 open and responding.
* No recent critical log errors.

System health appears stable.

---

## 🚨 If This Worsens (Next Steps)

1. Restart service and monitor resource spike:

   ```bash
   sudo systemctl restart nginx
   ```

2. Increase logging level in nginx configuration and reload.

3. Capture deeper diagnostics:

   * `sudo strace -p <nginx-pid>`
   * `sudo tcpdump -i any port 80`
   * Enable extended monitoring (CloudWatch / metrics)

4. Check for high traffic or DDoS indicators.

---

## ✅ Summary

This drill established a repeatable troubleshooting routine:

* Verify environment
* Capture CPU & memory state
* Check disk & IO
* Validate network exposure
* Inspect logs
* Define escalation steps

This runbook can be reused during real production incidents.
