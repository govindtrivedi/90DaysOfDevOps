# Day 11 Challenge – File Ownership

## Files & Directories Created

### Users

* `tokyo`
* `berlin`
* `nairobi`

### Groups

* `heist-team`
* `planners`
* `vault-team`
* `tech-team`

### Files & Directories

* `devops-file.txt`
* `team-notes.txt`
* `project-config.yaml`
* `app-logs/`
* `heist-project/`
* `bank-heist/`

---

## Task 1: Understanding Ownership

### Command

```bash
ls -l
```

Example format:

```text
-rw-r--r-- 1 owner group size date filename
```

**Observation:**
The **owner** is the user who owns the file. The **group** identifies the group associated with the file. Ownership and group membership are used together with Linux permissions to control access.

**Screenshot:**
*Add screenshot of `ls -l` output.*

---

## Task 2: Basic `chown`

### Create File

```bash
touch devops-file.txt
ls -l devops-file.txt
```

**Before:**

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

### Change Owner to `tokyo`

```bash
sudo chown tokyo devops-file.txt
ls -l devops-file.txt
```

### Change Owner to `berlin`

```bash
sudo chown berlin devops-file.txt
ls -l devops-file.txt
```

**Observation:**
The file owner was changed first to `tokyo` and then to `berlin`. The group remained unchanged.

**Screenshot:**
*Add before/after ownership screenshot.*

---

## Task 3: Basic `chgrp`

### Create File

```bash
touch team-notes.txt
ls -l team-notes.txt
```

### Create Group

```bash
sudo groupadd heist-team
```

### Change Group

```bash
sudo chgrp heist-team team-notes.txt
```

### Verify

```bash
ls -l team-notes.txt
```

**Observation:**
The group ownership of `team-notes.txt` was changed to `heist-team` while the file owner remained unchanged.

**Screenshot:**
*Add screenshot showing `heist-team` as the group.*

---

## Task 4: Change Owner and Group Together

### Create File

```bash
touch project-config.yaml
```

### Change Owner and Group

```bash
sudo chown professor:heist-team project-config.yaml
```

### Verify

```bash
ls -l project-config.yaml
```

**Observation:**
The owner was changed to `professor` and the group was changed to `heist-team` in a single command.

### Create `app-logs`

```bash
mkdir app-logs
sudo chown berlin:heist-team app-logs
ls -ld app-logs
```

**Observation:**
The `app-logs` directory is owned by `berlin` and belongs to the `heist-team` group.

---

## Task 5: Recursive Ownership

### Create Directory Structure

```bash
mkdir -p heist-project/vault
mkdir -p heist-project/plans

touch heist-project/vault/gold.txt
touch heist-project/plans/strategy.conf
```

### Create Group

```bash
sudo groupadd planners
```

### Change Ownership Recursively

```bash
sudo chown -R professor:planners heist-project/
```

### Verify

```bash
ls -lR heist-project/
```

**Observation:**
The `-R` option recursively changed the owner and group of the `heist-project` directory and everything inside it.

**Screenshot:**
*Add screenshot of recursive `ls -lR` output.*

---

## Task 6: Practice Challenge

### Create Users if Required

```bash
sudo useradd -m tokyo
sudo useradd -m berlin
sudo useradd -m nairobi
```

If the users already exist, do not run these commands again.

### Create Groups

```bash
sudo groupadd vault-team
sudo groupadd tech-team
```

If the groups already exist, continue to the next step.

### Create Directory and Files

```bash
mkdir bank-heist

touch bank-heist/access-codes.txt
touch bank-heist/blueprints.pdf
touch bank-heist/escape-plan.txt
```

### Set Ownership

```bash
sudo chown tokyo:vault-team bank-heist/access-codes.txt
sudo chown berlin:tech-team bank-heist/blueprints.pdf
sudo chown nairobi:vault-team bank-heist/escape-plan.txt
```

### Verify

```bash
ls -l bank-heist/
```

**Expected ownership:**

| File               | Owner     | Group        |
| ------------------ | --------- | ------------ |
| `access-codes.txt` | `tokyo`   | `vault-team` |
| `blueprints.pdf`   | `berlin`  | `tech-team`  |
| `escape-plan.txt`  | `nairobi` | `vault-team` |

**Screenshot:**
*Add screenshot of `ls -l bank-heist/`.*

---

## Ownership Changes Summary

| File/Directory        | Owner          | Group          |
| --------------------- | -------------- | -------------- |
| `devops-file.txt`     | `berlin`       | Original group |
| `team-notes.txt`      | Original owner | `heist-team`   |
| `project-config.yaml` | `professor`    | `heist-team`   |
| `app-logs/`           | `berlin`       | `heist-team`   |
| `heist-project/`      | `professor`    | `planners`     |
| `access-codes.txt`    | `tokyo`        | `vault-team`   |
| `blueprints.pdf`      | `berlin`       | `tech-team`    |
| `escape-plan.txt`     | `nairobi`      | `vault-team`   |

---

## Commands Used

```bash
ls -l
touch
mkdir
groupadd
useradd
chown
chgrp
ls -ld
ls -lR
```

Important ownership commands:

```bash
sudo chown user file
sudo chgrp group file
sudo chown user:group file
sudo chown -R user:group directory/
```

---

## What I Learned

1. Linux files have both a **user owner and group owner**, which work with permissions to control access.
2. `chown` changes the file owner, while `chgrp` changes the group ownership.
3. `chown -R` applies ownership changes recursively to a directory and all of its contents.

---

## Final Verification

```bash
ls -l devops-file.txt
ls -l team-notes.txt
ls -l project-config.yaml
ls -ld app-logs
ls -lR heist-project/
ls -l bank-heist/
```

**Final Status:**
All required ownership and group changes were completed and verified.

---

## Screenshots

1. Home directory ownership using `ls -l`
2. `devops-file.txt` before and after `chown`
3. `team-notes.txt` after `chgrp`
4. `project-config.yaml` and `app-logs/` ownership
5. Recursive `heist-project/` ownership
6. Final `bank-heist/` ownership
