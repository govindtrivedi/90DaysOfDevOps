# Day 10 Challenge – File Permissions & File Operations

## Files Created

### 1. `devops.txt`

Created an empty file using `touch`.

```bash
touch devops.txt
```

### 2. `notes.txt`

Created a file with sample content.

```bash
echo "Linux file permissions are important in DevOps." > notes.txt
```

### 3. `script.sh`

Created the script using `vim`.

```bash
vim script.sh
```

Content:

```bash
echo "Hello DevOps"
```

### Verify Files

```bash
ls -l devops.txt notes.txt script.sh
```

**Screenshot:**
*Add screenshot of the `ls -l` output here.*

---

## Read Files

### Read `notes.txt`

```bash
cat notes.txt
```

**Observation:**
The contents of `notes.txt` were displayed successfully.

### View `script.sh`

```bash
vim -R script.sh
```

**Observation:**
The script was opened in read-only mode.

### First 5 lines of `/etc/passwd`

```bash
head -n 5 /etc/passwd
```

**Observation:**
The first five lines of `/etc/passwd` were displayed.

### Last 5 lines of `/etc/passwd`

```bash
tail -n 5 /etc/passwd
```

**Observation:**
The last five lines of `/etc/passwd` were displayed.

---

## Understanding Linux Permissions

Linux permissions use the following format:

```text
rwxrwxrwx
```

They are divided into:

```text
Owner   Group   Others
rwx     rwx     rwx
```

Permission values:

| Permission    | Value |
| ------------- | ----: |
| Read (`r`)    |     4 |
| Write (`w`)   |     2 |
| Execute (`x`) |     1 |

### Check Current Permissions

```bash
ls -l devops.txt notes.txt script.sh
```

**Actual permissions from my system:**

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

### Permission Analysis

* `r` = file can be read
* `w` = file can be modified
* `x` = file can be executed
* Owner permissions apply to the file owner.
* Group permissions apply to members of the file's group.
* Others permissions apply to everyone else.

---

## Permission Changes

### 1. Make `script.sh` Executable

```bash
chmod +x script.sh
ls -l script.sh
```

Run the script:

```bash
./script.sh
```

**Output:**

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Observation:**
The execute permission was added and the script could be executed directly.

---

### 2. Make `devops.txt` Read-Only

```bash
chmod a-w devops.txt
ls -l devops.txt
```

**Observation:**
Write permission was removed for the owner, group, and others.

---

### 3. Set `notes.txt` to `640`

```bash
chmod 640 notes.txt
ls -l notes.txt
```

`640` means:

```text
Owner  = rw-  → 6
Group  = r--  → 4
Others = ---  → 0
```

**Observation:**
The owner can read and write, the group can read, and others have no permissions.

---

### 4. Create `project/` with `755`

```bash
mkdir project
chmod 755 project
ls -ld project
```

`755` means:

```text
Owner  = rwx → 7
Group  = r-x → 5
Others = r-x → 5
```

**Observation:**
The owner has full permissions, while group members and others can read and access the directory but cannot modify its contents unless file permissions allow it.

**Screenshot:**
*Add screenshot showing the final permissions.*

---

## Testing Permissions

### Test 1: Write to Read-Only File

```bash
echo "test" >> devops.txt
```

**Actual output:**

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Observation:**
The write operation was denied when the current user did not have write permission.

---

### Test 2: Execute Without Execute Permission

First remove execute permission:

```bash
chmod -x script.sh
```

Then try:

```bash
./script.sh
```

**Actual output:**

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Observation:**
The script could not be executed directly because execute permission was removed.

---

## Permission Summary

| File/Directory | Required Permission | Purpose                                         |
| -------------- | ------------------- | ----------------------------------------------- |
| `devops.txt`   | Read-only           | Prevent modification                            |
| `notes.txt`    | `640`               | Owner read/write, group read                    |
| `script.sh`    | Executable          | Allow direct execution                          |
| `project/`     | `755`               | Owner full access, group/others read and access |

---

## Commands Used

```bash
touch
echo
vim
vim -R
cat
head
tail
ls -l
chmod
mkdir
./script.sh
```

---

## What I Learned

1. Linux permissions control who can **read, write, and execute** files and directories.
2. `chmod` can modify permissions using both symbolic notation such as `+x` and numeric notation such as `640` and `755`.
3. Execute permission is required to run a script directly with `./script.sh`, while file and directory permissions determine what users can access or modify.

---

## Final Verification

```bash
ls -l devops.txt notes.txt script.sh
ls -ld project
```

**Screenshot:**
*Add final screenshot of all file and directory permissions.*

## Conclusion

I practiced creating and reading files, understanding Linux permission values, changing permissions with `chmod`, executing a shell script, and testing permission-denied scenarios.
