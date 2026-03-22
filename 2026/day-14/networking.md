# Day 14 – Networking Fundamentals & Hands-on Checks

## 🔹 Quick Concepts

### OSI vs TCP/IP

| Model | Layers | Details |
|-------|--------|---------|
| **OSI** | 7 layers (L1–L7) | Theoretical model with detailed layer separation |
| **TCP/IP** | 4 layers | Simplified real-world implementation |

**TCP/IP Layers:**
1. Link Layer
2. Internet Layer
3. Transport Layer
4. Application Layer

**Key Point:** TCP/IP is a simplified real-world implementation of OSI

### Protocol Placement

| Protocol | Layer | Function |
|----------|-------|----------|
| **IP** | Internet Layer | Routing and addressing |
| **TCP/UDP** | Transport Layer | Connection management and data flow |
| **HTTP/HTTPS** | Application Layer | Web communication |
| **DNS** | Application Layer | Domain name resolution |

### Real Example: `curl https://example.com`

```
Application (HTTP/HTTPS)
        ↓
Transport (TCP)
        ↓
Internet (IP)
        ↓
Link Layer (Physical)
```

---

## 🔹 Hands-on Checklist

### 1. Identity – What's My IP?

**Command:**
```bash
hostname -I
```

**Observation:** My system IP is `192.168.1.10`

**What it shows:** Your local network interface IP address(es)

---

### 2. Reachability – Can I Reach the Internet?

**Command:**
```bash
ping google.com
```

**Observation:** Low latency (~20ms), 0% packet loss → network is stable

**What it shows:**
- Connectivity to remote hosts
- Network latency (RTT - Round Trip Time)
- Packet loss percentage

**Interpretation:**
- ✅ < 50ms latency → Good
- ⚠️ 50-100ms latency → Acceptable
- ❌ > 100ms or packet loss → Potential issues

---

### 3. Path – How Do I Reach the Destination?

**Command:**
```bash
traceroute google.com
```

**Observation:** Multiple hops; slight delay at ISP level

**What it shows:**
- Route taken to reach destination
- Latency at each hop
- Network topology

**Pro Tip:** On Windows, use `tracert` instead of `traceroute`

---

### 4. Ports – What Services Are Running?

**Command:**
```bash
ss -tulpn
```

**Alternative:**
```bash
netstat -tulpn
```

**Observation:** SSH service running on port `22`

**Key Information:**
- `-t` → TCP connections
- `-u` → UDP connections
- `-l` → Listening ports only
- `-p` → Show process/program name
- `-n` → Numeric output (don't resolve names)

**Common Ports:**
| Port | Service |
|------|---------|
| 22 | SSH |
| 80 | HTTP |
| 443 | HTTPS |
| 3306 | MySQL |
| 5432 | PostgreSQL |

---

### 5. Name Resolution – Can I Resolve Domains?

**Command:**
```bash
nslookup google.com
```

**Alternative:**
```bash
dig google.com
```

**Observation:** Domain resolved to `142.x.x.x`

**What it shows:**
- DNS resolution working/failing
- IP address associated with domain
- DNS server used for query

**Troubleshooting DNS:**
```bash
# Check your DNS servers
cat /etc/resolv.conf

# Test with specific DNS server
nslookup google.com 8.8.8.8
```

---

### 6. HTTP Check – Is the Web Service Reachable?

**Command:**
```bash
curl -I https://google.com
```

**Observation:** HTTP 200 OK → service reachable

**What it shows:**
- HTTP response headers
- Status code (200, 404, 500, etc.)
- Server information

**Common HTTP Status Codes:**
| Code | Meaning | Issue |
|------|---------|-------|
| 200 | OK | ✅ Success |
| 404 | Not Found | ❌ Resource doesn't exist |
| 500 | Server Error | ❌ Application issue |
| 503 | Service Unavailable | ❌ Server down/overloaded |

**Full response with body:**
```bash
curl https://google.com
```

---

### 7. Connections Snapshot – What Connections Exist?

**Command:**
```bash
netstat -an | head
```

**Observation:** Few ESTABLISHED and LISTEN connections visible

**Connection States:**
| State | Meaning |
|-------|---------|
| **LISTEN** | Waiting for incoming connections |
| **ESTABLISHED** | Active connection |
| **TIME_WAIT** | Connection closed, waiting for cleanup |
| **SYN_SENT** | Initiating connection |
| **CLOSE_WAIT** | Waiting to close |

**Alternative (modern command):**
```bash
ss -an
```

---

## 🔹 Mini Task: Port Probe

### Check if a Specific Port is Reachable

**Command:**
```bash
nc -zv localhost 22
```

**Observation:** Connection successful → SSH port is reachable

**Breakdown:**
- `nc` → netcat tool
- `-z` → Zero-I/O mode (don't send data)
- `-v` → Verbose output

### If Not Reachable:

**Check service status:**
```bash
systemctl status ssh
```

**Check firewall rules:**
```bash
ufw status
```

**Allow port through firewall:**
```bash
ufw allow 22/tcp
```

**Check if service is listening:**
```bash
ss -tlnp | grep :22
```

---

## 🔹 Reflection

### Fastest Signal When Something Breaks

**Use `ping` first** → quickest way to check basic connectivity

**Why?**
- Minimal overhead
- Tests network layer (Layer 3)
- Immediate feedback
- No protocol complexity

```bash
ping -c 4 example.com  # Send 4 pings and stop
```

### Layer-Based Troubleshooting

#### Application Layer Issues
```
❌ DNS failure → Check DNS resolution
  $ nslookup example.com
  $ cat /etc/resolv.conf

❌ HTTP 500 error → Check server logs
  $ tail -f /var/log/apache2/error.log
  $ journalctl -u myapp
```

#### Transport Layer Issues
```
❌ Connection timeout → Check ports
  $ ss -tulpn | grep :80
  $ nc -zv example.com 80

❌ High latency → Check network path
  $ traceroute example.com
  $ mtr example.com (continuous monitoring)
```

#### Internet Layer Issues
```
❌ Can't reach host → Check IP routing
  $ ip route
  $ route -n

❌ Packet loss → Check link quality
  $ ping -c 100 example.com
  $ mtr example.com
```

---

## 🔹 Follow-up Checks in Real Incident

### 1. Check Service Logs

**System logs:**
```bash
tail -f /var/log/syslog
```

**Web server logs (Apache):**
```bash
tail -f /var/log/apache2/error.log
tail -f /var/log/apache2/access.log
```

**Web server logs (Nginx):**
```bash
tail -f /var/log/nginx/error.log
tail -f /var/log/nginx/access.log
```

**Application logs:**
```bash
journalctl -u myapp -f
```

### 2. Verify Firewall/Security Rules

**Check UFW status:**
```bash
ufw status verbose
```

**Check iptables rules:**
```bash
iptables -L -n -v
```

**Check network policies (Kubernetes):**
```bash
kubectl describe networkpolicy <policy-name>
```

### 3. Comprehensive Diagnostic Command

```bash
# All-in-one diagnosis
echo "=== Connectivity ===" && \
ping -c 1 google.com && \
echo "=== Ports ===" && \
ss -tulpn && \
echo "=== Routes ===" && \
ip route && \
echo "=== DNS ===" && \
cat /etc/resolv.conf && \
echo "=== Firewall ===" && \
ufw status
```

---

## 🔹 Quick Reference Card

### Commands Cheat Sheet

```bash
# Identity
hostname -I                      # Your local IP

# Connectivity
ping google.com                  # Test reachability
traceroute google.com            # Show route path
mtr google.com                   # Continuous route monitoring

# Ports & Services
ss -tulpn                        # Show all listening ports
netstat -an                      # Show all connections
nc -zv localhost 22              # Test specific port

# DNS Resolution
nslookup example.com             # Query DNS
dig example.com                  # Detailed DNS query
cat /etc/resolv.conf             # View DNS servers

# HTTP/Web
curl -I https://example.com      # Check HTTP headers
curl https://example.com         # Full response
wget https://example.com         # Download file

# Logging
tail -f /var/log/syslog          # Follow system logs
journalctl -u service -f         # Follow service logs
```

---

**Last Updated:** Day 14 of Networking Fundamentals Course
