# Day 15 – Networking Concepts: DNS, IP, Subnets & Ports

> **Learning Path:** Building foundational networking knowledge essential for every DevOps engineer.

---

## Task 1: DNS – How Names Become IPs

### What Happens When You Type google.com in a Browser?

When you enter `google.com` in your browser, the following sequence occurs:

1. **Local Cache Check**: Your computer first checks its local DNS cache to see if it already knows the IP address for google.com
2. **Recursive Query**: If not cached, your device sends a recursive DNS query to your configured DNS resolver (usually your ISP's resolver or 8.8.8.8)
3. **Hierarchical Resolution**: The resolver queries the root nameserver, then the TLD nameserver (.com), then the authoritative nameserver for google.com
4. **IP Resolution**: The authoritative nameserver responds with the IP address (e.g., 142.251.32.14), which gets cached and returned to your browser
5. **Connection Established**: Your browser now uses this IP address to establish a TCP connection and fetch the webpage

The entire process typically takes milliseconds, and results are cached at multiple levels to reduce latency.

### DNS Record Types

| Record Type | Purpose |
|---|---|
| **A** | Maps a domain name to an IPv4 address (e.g., google.com → 142.251.32.14) |
| **AAAA** | Maps a domain name to an IPv6 address for modern internet protocol support |
| **CNAME** | Creates an alias for another domain name (e.g., www.google.com → google.com) |
| **MX** | Specifies mail exchange servers that handle email for the domain |
| **NS** | Points to the authoritative nameservers responsible for the domain's DNS records |

### DNS Lookup Example (Simulated Output)

```
; <<>> DiG 9.16.1 <<>> google.com
;; Query time: 45 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)

google.com.             299     IN      A       142.251.32.14
google.com.             299     IN      A       142.251.32.78

;; ANSWER SECTION:
;google.com.            299     IN      A       142.251.32.14

;; AUTHORITY SECTION:
;google.com.            172800  IN      NS      ns1.google.com.
;google.com.            172800  IN      NS      ns2.google.com.
```

**Key Observations:**
- **A Record**: `google.com` resolves to IP `142.251.32.14`
- **TTL (Time To Live)**: 299 seconds - the duration this record will be cached before being re-queried
- **Multiple A Records**: Google uses multiple IPs for load distribution and redundancy

---

## Task 2: IP Addressing

### What is an IPv4 Address? How is it Structured?

An **IPv4 address** is a 32-bit identifier that uniquely identifies a device on the internet or a network. It's expressed as four decimal numbers separated by dots.

**Structure: `192.168.1.10`**

- Each number represents an **octet** (8 bits) and ranges from 0-255
- Total combinations: 2³² = 4,294,967,296 possible addresses
- Written in **dotted decimal notation** for human readability
- In binary: `11000000.10101000.00000001.00001010`

**Breaking Down 192.168.1.10:**
- **192**: Identifies the first network segment (0-255)
- **168**: Identifies the second network segment (0-255)
- **1**: Identifies the third network segment (0-255)
- **10**: Identifies the host on the network (0-255)

### Public vs Private IP Addresses

| Aspect | Public IP | Private IP |
|---|---|---|
| **Routing** | Globally routable on the internet | Not routable on the public internet |
| **Uniqueness** | Unique across the entire internet | Unique only within their private network |
| **Example** | 8.8.8.8 (Google DNS) | 192.168.1.1 (Home router) |
| **Cost** | Must be leased from an ISP | Free to use within organizations |
| **Visibility** | Visible to all internet servers | Invisible outside the local network |
| **NAT Required** | No translation needed | Requires NAT to access the internet |

**Real-World Example:**
- **Public IP**: Your ISP assigns your home router a public IP (e.g., 203.0.113.45) visible to the entire internet
- **Private IP**: Your laptop gets a private IP from your router (e.g., 192.168.1.105) that only your home network can see

### Private IP Address Ranges (RFC 1918)

These ranges are reserved for private use and will never be assigned as public IPs:

```
10.0.0.0       to  10.255.255.255    (10.0.0.0/8)       — Class A Private
172.16.0.0     to  172.31.255.255    (172.16.0.0/12)    — Class B Private
192.168.0.0    to  192.168.255.255   (192.168.0.0/16)   — Class C Private
```

**Characteristic:** Any device on the internet will ignore packets from these ranges, ensuring they're only used internally.

### IP Configuration Example (Simulated)

```bash
$ ip addr show

1: lo: <LOOPBACK,UP,LOWER_UP>
    inet 127.0.0.1/8 scope host lo          ← Private (loopback)
    inet6 ::1/128 scope host                ← IPv6 loopback

2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP>
    inet 203.0.113.45/24 brd 203.0.113.255 scope global eth0    ← Public IP
    inet6 fe80::42:1aff:fe52:29d0/64 scope link                 ← IPv6 link-local

3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP>
    inet 172.17.0.1/16 scope global docker0    ← Private (Docker network)

4: veth1234567: <BROADCAST,MULTICAST,UP,LOWER_UP>
    inet 192.168.1.100/24 scope global        ← Private (Home network range)
```

**Private IPs Identified:**
- `127.0.0.1` - Loopback interface (always private)
- `172.17.0.1` - Docker network (Class B private range)
- `192.168.1.100` - Local network (Class C private range)

---

## Task 3: CIDR & Subnetting

### What Does /24 Mean in 192.168.1.0/24?

**CIDR (Classless Inter-Domain Routing)** is a notation that combines an IP address with a subnet mask.

**Format: `IP_ADDRESS/PREFIX`**

- **192.168.1.0** = The network address
- **/24** = The number of bits reserved for the network portion (out of 32 total bits)
- **Remaining bits** = 32 - 24 = 8 bits for host addresses

**Converting /24 to Subnet Mask:**
- /24 means the first 24 bits identify the network: `11111111.11111111.11111111.00000000`
- In decimal: **255.255.255.0**
- This network can contain 2⁸ = **256 total addresses**

### How Many Usable Hosts?

| CIDR | Subnet Mask | Total IPs | Usable Hosts | Network | Broadcast |
|---|---|---|---|---|---|
| /24 | 255.255.255.0 | 256 | **254** | x.x.x.0 | x.x.x.255 |
| /16 | 255.255.0.0 | 65,536 | **65,534** | x.x.0.0 | x.x.255.255 |
| /28 | 255.255.255.240 | 16 | **14** | x.x.x.0 | x.x.x.15 |

**Why Not All IPs Are Usable:**
- **First address** (network address): 192.168.1.**0** — identifies the network itself
- **Last address** (broadcast address): 192.168.1.**255** — for broadcasting to all hosts
- **Usable host range**: 192.168.1.1 through 192.168.1.254

### Why Do We Subnet?

Subnetting divides large networks into smaller, more manageable pieces:

1. **Network Organization**: Separate departments, locations, or environments into logical groups
2. **Reduced Broadcasting**: Broadcast traffic is limited to each subnet, reducing network congestion
3. **Security Isolation**: Easier to apply access controls between subnets
4. **IP Efficiency**: Use only the number of IPs you actually need (e.g., /28 for a 10-host network instead of /24)
5. **Routing Efficiency**: Smaller subnets reduce routing table size and improve lookup speed
6. **Multi-tenancy**: Cloud providers use subnetting to isolate customer networks

**Example:** Instead of giving every company department a /16 network (65,534 hosts), split it into /24 subnets (254 hosts each). A /16 can fit 256 /24 subnets.

### CIDR & Subnetting Reference Table

```
CIDR	  Subnet Mask         Total IPs   Usable Hosts   Use Case
────────────────────────────────────────────────────────────────
/32   255.255.255.255           1            1        Single host route
/31   255.255.255.254           2            2        Point-to-point links (RFC 3021)
/28   255.255.255.240          16           14        Small server clusters
/27   255.255.255.224          32           30        Small department network
/26   255.255.255.192          64           62        Medium office network
/24   255.255.255.0           256          254        Typical LAN, AWS subnet
/16   255.255.0.0          65,536       65,534        Large organization, VPC
/8    255.0.0.0         16,777,216   16,777,214      Massive ISP allocation
```

---

## Task 4: Ports – The Doors to Services

### What is a Port? Why Do We Need Them?

A **port** is a logical endpoint of a network connection that identifies a specific process or service on a device. Ports operate at **Layer 4 (Transport Layer)** of the OSI model.

**Why We Need Ports:**
- **Multiplexing**: A single IP can run multiple services simultaneously
- **Service Identification**: Clients know which port to connect to for a specific service
- **Process Isolation**: Different services bind to different ports, preventing conflicts
- **Stateless Communication**: Port numbers are part of the packet header, enabling routing and firewalling

**Port Range:**
- **0-65535** total ports available (2¹⁶ possibilities)
- **0-1023** = Well-known ports (require admin/root privileges)
- **1024-49151** = Registered ports (user/application ports)
- **49152-65535** = Dynamic/private ports (temporary, ephemeral)

### Common Ports Reference

| Port | Protocol | Service | Purpose |
|---|---|---|---|
| **22** | SSH | Secure Shell | Remote terminal access, secure file transfer (scp, sftp) |
| **80** | HTTP | HyperText Transfer Protocol | Unencrypted web traffic, legacy websites |
| **443** | HTTPS | HTTP Secure | Encrypted web traffic, modern websites (TLS/SSL) |
| **53** | DNS | Domain Name System | Translates domain names to IP addresses (UDP/TCP) |
| **3306** | MySQL | MySQL Database | Relational database server for applications |
| **6379** | Redis | Redis Cache | In-memory data structure store, caching layer |
| **27017** | MongoDB | MongoDB Database | NoSQL document database server |
| **25** | SMTP | Simple Mail Transfer Protocol | Outbound email transmission |
| **110** | POP3 | Post Office Protocol | Incoming email retrieval (deprecated, use IMAP) |
| **143** | IMAP | Internet Message Access Protocol | Modern incoming email protocol |
| **5432** | PostgreSQL | PostgreSQL Database | Advanced relational database server |
| **6443** | HTTPS | Kubernetes API | Kubernetes API server communication |
| **8080** | HTTP | HTTP Alternate | Common application web server port |
| **3389** | RDP | Remote Desktop Protocol | Windows remote desktop connections |
| **1433** | TDS | MS SQL Server | Microsoft SQL Server database |
| **5900** | VNC | Virtual Network Computing | Remote desktop for Linux/Unix |

### Listening Ports Example (Simulated)

```bash
$ ss -tulpn

Proto Recv-Q Send-Q Local Address      Foreign Address    State      PID/Program name
tcp        0      0  0.0.0.0:22        0.0.0.0:*          LISTEN     1234/sshd
tcp        0      0  127.0.0.1:3306    0.0.0.0:*          LISTEN     5678/mysqld
tcp        0      0  0.0.0.0:80        0.0.0.0:*          LISTEN     9012/nginx
tcp        0      0  0.0.0.0:443       0.0.0.0:*          LISTEN     9012/nginx
tcp        0      0  127.0.0.1:6379    0.0.0.0:*          LISTEN     3456/redis-server
tcp6       0      0  :::8080           :::*               LISTEN     7890/java
```

**Matched Services:**
1. **Port 22 (SSH)** ← sshd process is listening for remote terminal connections
2. **Port 3306 (MySQL)** ← mysqld process is listening for database client connections
3. **Port 80/443 (HTTP/HTTPS)** ← nginx web server is listening for web traffic
4. **Port 6379 (Redis)** ← redis-server is listening for cache connections (localhost only, `127.0.0.1`)
5. **Port 8080 (HTTP)** ← Java application is listening for alternative HTTP connections

**Key Observation:** Redis binds to `127.0.0.1` (localhost only), meaning it's only accessible from the local machine, not from the network—a security practice for internal services.

---

## Task 5: Putting It Together

### Scenario 1: curl http://myapp.com:8080 — Involved Networking Concepts

When you run this command, the following networking concepts are in action:

1. **DNS Resolution** (Concept: Domain → IP)
   - Your system queries a DNS resolver for the IP address of `myapp.com`
   - The resolver returns an A record (e.g., `192.0.2.10`)

2. **IP Addressing & Routing** (Concept: Device Location)
   - Your computer uses the resolved IP to create packets destined for `192.0.2.10`
   - Network routers examine the IP address and forward packets along the path to the destination

3. **Port Targeting** (Concept: Service Identification)
   - The curl command specifies `:8080`, connecting to port 8080 on the remote server
   - The destination server has a web application (likely Node.js, Java, or similar) listening on port 8080

4. **TCP Connection** (Concept: Reliable Transport)
   - A TCP three-way handshake establishes a connection: SYN, SYN-ACK, ACK
   - HTTP request is sent over this established connection

5. **Response & Caching** (Concept: Efficiency)
   - DNS responses are cached locally to avoid repeated queries
   - HTTP responses may include cache headers (Cache-Control, ETag, etc.)

**Simplified Flow:** `myapp.com` (DNS) → `192.0.2.10` (IP Routing) → `:8080` (Port) → HTTP Request → Response

### Scenario 2: App Can't Reach Database at 10.0.1.50:3306 — What to Check First

When an application fails to connect to a remote database, check these in order:

1. **Verify Network Connectivity** (Most Common Issue: 80% of cases)
   ```bash
   ping 10.0.1.50          # Check if the host is reachable
   traceroute 10.0.1.50    # Trace the network path
   ```
   - **Issue**: Database server may be down, unreachable, or in a different network segment
   - **Solution**: Verify the server is running, check routing tables, ensure the host is not isolated

2. **Verify Subnet Accessibility** (CIDR & Subnetting Issue)
   - Check if `10.0.1.50` is in the same subnet as your app's IP
   - Example: If your app is `10.0.1.100/24` and database is `10.0.1.50/24`, they're in the same subnet ✓
   - Example: If your app is `10.0.1.100/25` and database is `10.0.2.50/25`, they're in different subnets ✗
   - **Solution**: Verify subnet masks and routing between subnets

3. **Check Port Accessibility** (Port & Firewall Issue)
   ```bash
   telnet 10.0.1.50 3306   # Check if port 3306 is open
   nc -zv 10.0.1.50 3306   # netcat test connection
   ```
   - **Issue**: Database service not running, firewall blocking port 3306, or wrong port number
   - **Solution**: Start the MySQL service, verify firewall rules, confirm port number

4. **Verify DNS Resolution** (DNS Issue, if using hostname)
   ```bash
   nslookup database.internal   # Resolve hostname to IP
   ```
   - **Issue**: If connecting via hostname instead of IP, DNS may be returning wrong IP or timing out
   - **Solution**: Use direct IP, verify DNS configuration, check /etc/hosts file

5. **Check Security Groups & Network Policies** (Cloud Infrastructure Issue)
   - AWS Security Groups, Azure NSGs, Kubernetes Network Policies may be blocking traffic
   - **Solution**: Add inbound rule for port 3306 from the app's security group to the database's security group

**Quick Checklist:**
```
☐ Database server running? (systemctl status mysql)
☐ Database listening on 3306? (netstat -tlnp | grep 3306)
☐ Network connectivity exists? (ping 10.0.1.50)
☐ Port 3306 accessible? (telnet 10.0.1.50 3306)
☐ Firewall allows port 3306? (iptables -L, cloud security groups)
☐ Correct credentials? (mysql -h 10.0.1.50 -u user -p)
```

---

## Key Learnings

### 1. DNS is the Internet's Address Book
DNS translates human-readable domain names into IP addresses in milliseconds. Understanding DNS records (A, AAAA, CNAME, MX, NS), TTLs, and recursive resolution is critical for troubleshooting web applications and email systems. DevOps engineers should be familiar with DNS propagation delays and DNS caching strategies.

### 2. Subnetting Enables Network Organization at Scale
CIDR notation and subnetting are fundamental to modern cloud infrastructure. Correctly sizing subnets (using appropriate prefix lengths) prevents IP exhaustion, improves security through isolation, and reduces broadcast traffic. Understanding /24, /16, and /28 networks helps when designing VPCs, Kubernetes networks, and on-premises infrastructure.

### 3. Ports and Services Are the Application Layer Foundation
Ports identify which service a packet should be delivered to. From SSH (22) to HTTP (80) to databases (3306, 5432, 27017), knowing common ports and how to verify listening services is essential for debugging connectivity issues, securing applications, and understanding application architecture.

---

## Commands Used

### Attempted Commands:
```bash
# DNS Resolution (would require dig installed)
dig google.com
nslookup google.com

# IP Configuration
ip addr show
ifconfig

# Port Verification
ss -tulpn
netstat -tulpn
telnet 10.0.1.50 3306
nc -zv 10.0.1.50 3306

# Network Testing
ping 10.0.1.50
traceroute 10.0.1.50
```

### Note:
In the current Linux environment, advanced networking tools (dig, nslookup, ip, ss) are not available, but this documentation provides the expected output and interpretation of these commands for real-world usage.

---

## Summary Table

| Concept | Key Point | Example |
|---|---|---|
| **DNS** | Translates names to IPs | google.com → 142.251.32.14 |
| **IPv4 Address** | 32-bit identifier for devices | 192.168.1.10 |
| **Private IP Ranges** | RFC 1918 reserved ranges | 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 |
| **CIDR Notation** | IP + network bits | 192.168.1.0/24 |
| **/24 Network** | 256 total IPs, 254 usable | Typical LAN, AWS subnet |
| **/16 Network** | 65,536 total IPs, 65,534 usable | Large organization, VPC |
| **Subnetting** | Divides networks for organization & security | Isolate departments, improve efficiency |
| **Port** | Identifies a service on a device | Port 22 = SSH, Port 80 = HTTP |
| **Well-Known Ports** | 0-1023, require admin privileges | SSH (22), HTTP (80), HTTPS (443), DNS (53) |
| **Troubleshooting** | Check connectivity, port, firewall, credentials | Use ping, telnet, netstat, security groups |

---

