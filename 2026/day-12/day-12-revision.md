# Day 12 – Breather & Revision

## 1. Mindset & Learning Plan

During Days 01–11, I focused on building Linux and DevOps fundamentals through hands-on practice.

### Topics Revised

* Linux basic commands
* Processes and services
* File and directory operations
* File permissions
* File ownership
* Users and groups
* `chmod`, `chown`, and `chgrp`
* Basic troubleshooting and system monitoring

### Learning Plan Update

My goal is still to build strong Linux fundamentals and gradually move toward **DevOps, Cloud, and Infrastructure Engineering**.

For the next few days, I will focus more on troubleshooting, networking, and automation.

---

## 2. Processes & Services

### Command 1: Check Processes

```bash
ps aux | head
```

**Observation:**
This command displays currently running processes. I can use it to quickly identify processes and check their resource usage.

### Command 2: Check Service Status

```bash
systemctl status ssh --no-pager
```

**Observation:**
This command helps verify whether the SSH service is running and provides recent service information.

> If my system uses `sshd` instead of `ssh`, I will use `systemctl status sshd --no-pager`.

---

## 3. File Skills

### Append Content

```bash
echo "Day 12 revision" >> notes.txt
```

This adds new content to the end of the file without overwriting existing content.

### Check Permissions

```bash
ls -l notes.txt
```

This shows the file permissions, owner, group, size, and other information.

### Change Permission

```bash
chmod 640 notes.txt
```

This gives the owner read/write permission, the group read permission, and no permissions to others.

### Change Ownership

```bash
sudo chown tokyo:developers notes.txt
```

This changes the owner to `tokyo` and the group to `developers`.

---

## 4. Five Commands I Would Reach for First

1. **`ps`** – Quickly check running processes and resource usage.
2. **`systemctl status`** – Check whether a service is running correctly.
3. **`journalctl`** – Review service logs and identify errors.
4. **`df -h`** – Quickly check available disk space.
5. **`free -h`** – Check memory usage and available RAM.

---

## 5. User & Group Sanity

### Check User

```bash
id tokyo
```

**Observation:**
The command displays the user's UID, primary group, and supplementary group memberships.

### Check File Ownership

```bash
ls -l notes.txt
```

**Observation:**
The output shows the current owner and group of the file.

### Small Ownership Scenario

```bash
sudo chown tokyo:developers notes.txt
ls -l notes.txt
```

**Observation:**
The file ownership was changed and verified using `ls -l`.

---

# Mini Self-Check

## 1. Which 3 commands save you the most time right now, and why?

### `systemctl status`

It quickly tells me whether a service is running and provides useful status information.

### `journalctl`

It helps me find service errors and understand what happened before troubleshooting further.

### `df -h`

It quickly shows filesystem usage and helps identify disk-space problems.

---

## 2. How do you check if a service is healthy?

The first commands I would run are:

```bash
systemctl status ssh --no-pager
```

```bash
journalctl -u ssh -n 50 --no-pager
```

```bash
ps aux | grep ssh
```

These commands help me check the service status, recent logs, and running process.

---

## 3. How do you safely change ownership and permissions?

First, I would check the existing ownership and permissions:

```bash
ls -l notes.txt
```

Then I would make the required change:

```bash
sudo chown tokyo:developers notes.txt
chmod 640 notes.txt
```

Finally, I would verify the changes:

```bash
ls -l notes.txt
```

This helps avoid accidentally changing the wrong file or applying incorrect permissions.

---

## 4. What Will I Focus on Improving in the Next 3 Days?

Over the next three days, I will focus on:

* Linux troubleshooting
* Networking fundamentals
* Shell scripting and automation
* Understanding services and logs
* Improving confidence with real-world DevOps troubleshooting

---

# Key Takeaways

* I learned that troubleshooting should start by **collecting evidence before making changes**.
* Linux permissions control access using **owner, group, and others**.
* `chown` changes ownership while `chmod` changes permissions.
* Service status and logs are important starting points when investigating problems.
* Regular hands-on practice makes Linux commands easier to remember.

---

# Final Checkpoint

* [x] Reviewed Linux fundamentals
* [x] Reviewed processes and services
* [x] Practiced file operations
* [x] Reviewed permissions and ownership
* [x] Reviewed users and groups
* [x] Practiced troubleshooting commands
* [x] Completed self-check questions

**Status:** Day 12 revision completed.
