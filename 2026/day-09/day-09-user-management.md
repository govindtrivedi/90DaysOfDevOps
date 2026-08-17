# Day 09 Challenge – Linux User & Group Management

## Users & Groups Created

### Users

* `tokyo`
* `berlin`
* `professor`
* `nairobi`

### Groups

* `developers`
* `admins`
* `project-team`

---

## Task 1: Create Users

### Commands Used

```bash
sudo useradd -m tokyo
sudo passwd tokyo

sudo useradd -m berlin
sudo passwd berlin

sudo useradd -m professor
sudo passwd professor

sudo useradd -m nairobi
sudo passwd nairobi
```

### Verification

```bash
grep -E '^(tokyo|berlin|professor|nairobi):' /etc/passwd
ls -ld /home/tokyo /home/berlin /home/professor /home/nairobi
```

### Observation

All four users were created with home directories. Passwords were configured successfully.

**Screenshot:**
*Add screenshot of `/etc/passwd` and `/home` verification here.*

---

## Task 2: Create Groups

### Commands Used

```bash
sudo groupadd developers
sudo groupadd admins
sudo groupadd project-team
```

### Verification

```bash
grep -E '^(developers|admins|project-team):' /etc/group
```

### Observation

The required groups were created successfully.

**Screenshot:**
*Add screenshot of `/etc/group` verification here.*

---

## Task 3: Assign Users to Groups

### Commands Used

```bash
sudo usermod -aG developers tokyo

sudo usermod -aG developers,admins berlin

sudo usermod -aG admins professor

sudo usermod -aG project-team nairobi
sudo usermod -aG project-team tokyo
```

### Verification

```bash
groups tokyo
groups berlin
groups professor
groups nairobi
```

### Expected Group Assignments

| User        | Groups                       |
| ----------- | ---------------------------- |
| `tokyo`     | `developers`, `project-team` |
| `berlin`    | `developers`, `admins`       |
| `professor` | `admins`                     |
| `nairobi`   | `project-team`               |

**Screenshot:**
*Add screenshot showing the actual `groups` output.*

---

## Task 4: Shared Developer Directory

### Create Directory

```bash
sudo mkdir -p /opt/dev-project
```

### Set Group Ownership

```bash
sudo chgrp developers /opt/dev-project
```

### Set Permissions

```bash
sudo chmod 775 /opt/dev-project
```

### Verify

```bash
ls -ld /opt/dev-project
```

### Test File Creation

```bash
sudo -u tokyo touch /opt/dev-project/tokyo-file
sudo -u berlin touch /opt/dev-project/berlin-file

ls -l /opt/dev-project
```

### Observation

The directory is owned by the `developers` group and has `775` permissions. Both `tokyo` and `berlin` should be able to create files because both users belong to `developers`.

**Screenshot:**
*Add screenshot of directory permissions and test files.*

---

## Task 5: Team Workspace

### Create Directory

```bash
sudo mkdir -p /opt/team-workspace
```

### Set Group Ownership

```bash
sudo chgrp project-team /opt/team-workspace
```

### Set Permissions

```bash
sudo chmod 775 /opt/team-workspace
```

### Verify

```bash
ls -ld /opt/team-workspace
```

### Test as Nairobi

```bash
sudo -u nairobi touch /opt/team-workspace/nairobi-file
ls -l /opt/team-workspace
```

### Observation

The workspace is owned by the `project-team` group and uses `775` permissions. `nairobi` can create files because the user was added to `project-team`.

**Screenshot:**
*Add screenshot of permissions and Nairobi's test file.*

---

## Group Assignments

* **tokyo** → `developers`, `project-team`
* **berlin** → `developers`, `admins`
* **professor** → `admins`
* **nairobi** → `project-team`

---

## Directories Created

| Directory             | Group          | Permissions |
| --------------------- | -------------- | ----------- |
| `/opt/dev-project`    | `developers`   | `775`       |
| `/opt/team-workspace` | `project-team` | `775`       |

---

## Commands Used

```bash
useradd
passwd
groupadd
usermod
groups
grep
mkdir
chgrp
chmod
ls
sudo -u
```

---

## What I Learned

1. `useradd -m` creates a user along with their home directory.
2. `usermod -aG` can add an existing user to supplementary groups without removing their existing group memberships.
3. Linux group ownership and permissions can be used to create shared workspaces where multiple users can collaborate securely.

---

## Final Verification

```bash
groups tokyo
groups berlin
groups professor
groups nairobi

ls -ld /opt/dev-project
ls -ld /opt/team-workspace

ls -l /opt/dev-project
ls -l /opt/team-workspace
```

**Final Status:**
All required users, groups, group assignments, directories, permissions, and file-creation tests were completed successfully.

---

## Screenshots

1. `/etc/passwd` – users created
2. `/etc/group` – groups created
3. `groups username` – group membership
4. `/opt/dev-project` – permissions and test files
5. `/opt/team-workspace` – permissions and Nairobi test file
