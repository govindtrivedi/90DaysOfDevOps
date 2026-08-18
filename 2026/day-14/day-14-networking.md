# Day 14 – Networking Fundamentals & Hands-on Checks

## Objective

Today I practiced Linux networking fundamentals and troubleshooting commands. I checked my IP address, connectivity, network path, listening ports, DNS resolution, HTTP response, and active connections.

---

# 1. Networking Concepts

## OSI Model vs TCP/IP Model

| OSI Layer | Name         | TCP/IP Equivalent | Examples                   |
| --------- | ------------ | ----------------- | -------------------------- |
| L7        | Application  | Application       | HTTP, HTTPS, DNS           |
| L6        | Presentation | Application       | Data formatting/encryption |
| L5        | Session      | Application       | Session management         |
| L4        | Transport    | Transport         | TCP, UDP                   |
| L3        | Network      | Internet          | IP, ICMP                   |
| L2        | Data Link    | Link              | Ethernet, MAC              |
| L1        | Physical     | Link              | Cables, signals            |

### In my own words

* The **OSI model has 7 layers**, while the TCP/IP model commonly uses 4 layers: Link, Internet, Transport, and Application.
* **IP** works at the Network/Internet layer.
* **TCP and UDP** work at the Transport layer.
* **HTTP/HTTPS and DNS** are Application-layer protocols.

### Example

```text
curl https://example.com
        ↓
HTTPS / HTTP
        ↓
TCP
        ↓
IP
        ↓
Network Interface
```

---

# 2. Identity – Check IP Address

### Command

```bash
hostname -I
```

### Output

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

### Observation

The command displayed the IP address assigned to my machine. I can use this address to identify the host on the local network.

**Screenshot:**
*Add screenshot of the output.*

---

# 3. Reachability – Ping

### Command

```bash
ping -c 4 google.com
```

### Output

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

### Observation

The ping test was used to check whether the target host was reachable and to measure round-trip latency.

**Packet loss:** ______ %

**Average latency:** ______ ms

---

# 4. Path – Traceroute

### Command

```bash
traceroute google.com
```

If `traceroute` is not installed:

```bash
tracepath google.com
```

### Output

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

### Observation

The command displayed the network hops between my machine and the target. I checked for unusually long delays or timeout hops.

**Interesting finding:**

---

---

# 5. Listening Ports

### Command

```bash
ss -tulpn
```

### Output

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

### Observation

The command displayed TCP/UDP listening sockets and the processes using them.

### Listening service identified

**Service:** __________________

**Port:** __________________

**Protocol:** TCP / UDP

**Screenshot:**
*Add screenshot showing the listening port.*

---

# 6. DNS Name Resolution

### Command

```bash
dig google.com
```

If `dig` is unavailable:

```bash
nslookup google.com
```

### Output

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

### Resolved IP

```text
PASTE RESOLVED IP HERE
```

### Observation

DNS successfully translated the domain name into an IP address. This confirms that name resolution is working for the target.

---

# 7. HTTP Check

### Command

```bash
curl -I https://google.com
```

### Output

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

### HTTP Status

```text
HTTP STATUS: ______
```

### Observation

The HTTP request returned the status code shown above. A successful response indicates that the HTTP/HTTPS service is reachable.

---

# 8. Connections Snapshot

### Command

```bash
netstat -an | head
```

If `netstat` is unavailable:

```bash
ss -tan
```

### Output

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

### Observation

I used the output to identify connection states such as `ESTABLISHED` and `LISTEN`.

**ESTABLISHED connections observed:** ______

**LISTEN connections observed:** ______

---

# 9. Mini Task – Port Probe

## Step 1: Identify Listening Port

From `ss -tulpn`, I identified:

**Service:** __________________

**Port:** __________________

---

## Step 2: Test the Port

```bash
nc -zv localhost <PORT>
```

Replace `<PORT>` with the actual listening port.

Example:

```bash
nc -zv localhost 22
```

### Output

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

### Result

**Reachable:** Yes / No

### Observation

The port was reachable from the same machine, which indicates that a process is accepting connections on that port.

If the port was not reachable, my next checks would be:

```bash
systemctl status <service>
```

and:

```bash
sudo ss -tulpn
```

I would also check firewall rules if necessary.

---

# 10. Network Troubleshooting Summary

| Check           | Command                      | Result |
| --------------- | ---------------------------- | ------ |
| IP Address      | `hostname -I`                | ______ |
| Reachability    | `ping -c 4 google.com`       | ______ |
| Network Path    | `traceroute google.com`      | ______ |
| Listening Ports | `ss -tulpn`                  | ______ |
| DNS             | `dig google.com`             | ______ |
| HTTP            | `curl -I https://google.com` | ______ |
| Connections     | `netstat -an \| head`        | ______ |

---

# Reflection

## 1. Which command gives the fastest signal when something is broken?

For basic connectivity, I would start with:

```bash
ping -c 4 google.com
```

It quickly tells me whether the target is reachable and provides latency and packet-loss information.

For a specific web application, I would use:

```bash
curl -I https://example.com
```

because it checks HTTP/HTTPS connectivity and returns the HTTP status.

---

## 2. What layer would you inspect if DNS fails?

If DNS fails, I would investigate the **Application layer**, because DNS is an application-layer protocol.

I would check:

```bash
dig google.com
```

and verify the configured DNS resolver and network connectivity.

---

## 3. What if HTTP 500 appears?

An HTTP 500 response indicates a server-side application problem. I would investigate the application/server rather than assuming the network is down.

I would check:

```bash
curl -I https://example.com
```

Then review the application/web-server logs and service status.

---

# Two Follow-Up Checks in a Real Incident

### Check 1 – Service Status

```bash
systemctl status <service>
```

This tells me whether the relevant service is running.

### Check 2 – Service Logs

```bash
journalctl -u <service> -n 50 --no-pager
```

This helps identify recent errors or failures.

---

# Key Takeaways

* `ping` helps check basic network reachability and latency.
* `traceroute`/`tracepath` helps understand the path to a remote host.
* `ss -tulpn` helps identify listening services and ports.
* `dig`/`nslookup` helps troubleshoot DNS resolution.
* `curl` is useful for testing HTTP/HTTPS services.
* Network troubleshooting should be performed step-by-step instead of assuming the problem is with the application.

---

# Day 14 Status

* [x] Reviewed OSI and TCP/IP models
* [x] Checked local IP address
* [x] Tested network reachability
* [x] Checked network path
* [x] Checked listening ports
* [x] Tested DNS resolution
* [x] Tested HTTP response
* [x] Reviewed network connections
* [x] Completed port probe
* [x] Completed troubleshooting reflection

**Status: Day 14 Networking Fundamentals completed.**
