# Linux Troubleshooting Runbook

## Target Service / Process

**Target:** SSH service (`ssh` / `sshd`)

The goal of this drill was to capture a quick health snapshot of the system and review logs for the SSH service before taking any corrective action.

---

## 1. Environment Basics

### `uname -a`

```bash
uname -a
```

**Output:**

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Observation:**
Recorded the Linux kernel version, hostname, architecture, and system information.

### `cat /etc/os-release`

```bash
cat /etc/os-release
```

**Output:**

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Observation:**
Confirmed the Linux distribution and OS version.

---

## 2. Filesystem Sanity

### Create temporary test directory

```bash
mkdir -p /tmp/runbook-demo
echo "runbook test" > /tmp/runbook-demo/test.txt
ls -l /tmp/runbook-demo
```

**Output:**

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Observation:**
The temporary directory and test file were created successfully, confirming normal filesystem write access.

### Copy `/etc/hosts`

```bash
cp /etc/hosts /tmp/runbook-demo/hosts-copy
ls -l /tmp/runbook-demo
```

**Output:**

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Observation:**
The file was copied successfully, confirming normal file read/write operations.

---

## 3. CPU & Memory Snapshot

### CPU / Processes

```bash
ps -eo pid,pcpu,pmem,comm --sort=-pcpu | head
```

**Output:**

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Observation:**
Checked the processes with the highest CPU usage. No unusual CPU-consuming process was observed / `__________` was consuming the highest CPU.

### Memory

```bash
free -h
```

**Output:**

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Observation:**
Checked total, used, free, and available memory. Memory usage appeared normal / required investigation.

---

## 4. Disk & I/O Snapshot

### Disk Usage

```bash
df -h
```

**Output:**

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Observation:**
Checked filesystem utilization. No filesystem was critically full / `__________` showed high utilization.

### Log Directory Size

```bash
du -sh /var/log
```

**Output:**

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Observation:**
Checked the amount of disk space consumed by system logs.

---

## 5. Network Snapshot

### Listening Ports

```bash
ss -tulpn
```

**Output:**

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Observation:**
Checked listening ports and associated processes. Verified whether the SSH service was listening on the expected port.

### SSH Connectivity

```bash
ping -c 4 127.0.0.1
```

**Output:**

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Observation:**
Local network connectivity was tested. Packets were successfully received / packet loss was observed.

---

## 6. Logs Reviewed

### SSH Service Logs

Use the service name available on your system:

```bash
journalctl -u ssh -n 50 --no-pager
```

or:

```bash
journalctl -u sshd -n 50 --no-pager
```

**Output:**

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Observation:**
Reviewed the latest SSH logs for authentication failures, warnings, and service errors.

---

## 7. Quick Findings

* **CPU:** ______________________________
* **Memory:** ___________________________
* **Disk:** ______________________________
* **Network:** ___________________________
* **SSH service:** ________________________
* **Logs:** _______________________________

**Overall status:** Normal / Needs Investigation

---

## 8. If This Worsens

1. **Restart strategy:** Check the SSH service status and recent logs first. Restart the service only after collecting evidence about the problem.
2. **Increase log verbosity:** Increase SSH logging temporarily and reproduce the issue to collect more detailed information.
3. **Collect deeper diagnostics:** Use tools such as `strace` and additional `journalctl` output to investigate persistent process or service failures.

---

## 9. Cleanup

```bash
rm -rf /tmp/runbook-demo
```

Removed the temporary files created during the troubleshooting drill.
