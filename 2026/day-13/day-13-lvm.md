# Day 13 – Linux Volume Management (LVM)

## Objective

Today I practiced **Linux Logical Volume Management (LVM)**. I learned how to create a Physical Volume (PV), Volume Group (VG), and Logical Volume (LV), format and mount the volume, and extend the logical volume.

---

# Task 1: Check Current Storage

### Commands

```bash
lsblk
```

```bash
pvs
```

```bash
vgs
```

```bash
lvs
```

```bash
df -h
```

### Observation

I used these commands to understand the current disks, partitions, physical volumes, volume groups, logical volumes, and filesystem usage on my Linux system.

### Output

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Screenshot:**
*Add screenshot of `lsblk`, `pvs`, `vgs`, `lvs`, and `df -h`.*

---

# Task 2: Create Physical Volume

## Create a PV

I used a spare disk/loop device for the LVM practice.

First, I verified the device:

```bash
lsblk
```

Then I created the physical volume:

```bash
pvcreate /dev/loop0
```

> Replace `/dev/loop0` with the actual loop device shown by `losetup -a`.

### Verify

```bash
pvs
```

### Observation

The selected device was initialized as an LVM **Physical Volume (PV)**.

### Output

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Screenshot:**
*Add screenshot showing the PV.*

---

# Task 3: Create Volume Group

### Command

```bash
vgcreate devops-vg /dev/loop0
```

### Verify

```bash
vgs
```

### Observation

I created a Volume Group named `devops-vg`. The physical volume is now part of this volume group and can be used to create logical volumes.

### Output

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Screenshot:**
*Add screenshot of `vgs`.*

---

# Task 4: Create Logical Volume

### Command

```bash
lvcreate -L 500M -n app-data devops-vg
```

### Verify

```bash
lvs
```

### Observation

I created a **500 MB Logical Volume** named `app-data` inside the `devops-vg` volume group.

### Output

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Screenshot:**
*Add screenshot of `lvs`.*

---

# Task 5: Format and Mount

## Format the Logical Volume

```bash
mkfs.ext4 /dev/devops-vg/app-data
```

## Create Mount Point

```bash
mkdir -p /mnt/app-data
```

## Mount the Logical Volume

```bash
mount /dev/devops-vg/app-data /mnt/app-data
```

## Verify

```bash
df -h /mnt/app-data
```

Also verify the mount:

```bash
lsblk
```

### Observation

The logical volume was formatted with the **ext4 filesystem** and mounted at `/mnt/app-data`.

### Output

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Screenshot:**
*Add screenshot showing the mounted filesystem.*

---

# Task 6: Extend the Logical Volume

## Extend by 200 MB

```bash
lvextend -L +200M /dev/devops-vg/app-data
```

## Resize the ext4 Filesystem

```bash
resize2fs /dev/devops-vg/app-data
```

## Verify New Size

```bash
df -h /mnt/app-data
```

### Observation

I extended the logical volume by 200 MB and then resized the ext4 filesystem so that the additional storage became available to the mounted filesystem.

### Output

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Screenshot:**
*Add screenshot showing the increased filesystem size.*

---

# LVM Structure

The storage structure I created was:

```text
Physical Disk / Loop Device
        │
        ▼
Physical Volume (PV)
        │
        ▼
devops-vg
Volume Group (VG)
        │
        ▼
app-data
Logical Volume (LV)
        │
        ▼
ext4 filesystem
        │
        ▼
/mnt/app-data
```

---

# Useful LVM Commands

### Physical Volumes

```bash
pvs
pvdisplay
```

### Volume Groups

```bash
vgs
vgdisplay
```

### Logical Volumes

```bash
lvs
lvdisplay
```

### Storage Overview

```bash
lsblk
df -h
```

---

# What I Learned

### 1. Physical Volume

A **Physical Volume (PV)** is a disk or partition prepared for use by LVM.

Example:

```bash
pvcreate /dev/loop0
```

### 2. Volume Group

A **Volume Group (VG)** combines one or more physical volumes and provides a pool of storage.

Example:

```bash
vgcreate devops-vg /dev/loop0
```

### 3. Logical Volume

A **Logical Volume (LV)** is storage allocated from a volume group. It can be formatted with a filesystem, mounted, and extended when additional space is required.

Example:

```bash
lvcreate -L 500M -n app-data devops-vg
```

---

# Key Takeaways

* LVM provides flexible storage management by separating physical storage from logical volumes.
* Logical volumes can be **extended without repartitioning the underlying disk**.
* After extending an LV, the filesystem may also need to be resized. For ext4, `resize2fs` can be used.

---

# Final Verification

Run the following commands after completing the exercise:

```bash
lsblk
```

```bash
pvs
```

```bash
vgs
```

```bash
lvs
```

```bash
df -h /mnt/app-data
```

### Final Observation

```text
PASTE YOUR ACTUAL OUTPUT HERE
```

**Screenshot:**
*Add final screenshot showing the complete LVM setup.*

---

# Day 13 Status

* [x] Checked storage
* [x] Created Physical Volume
* [x] Created Volume Group
* [x] Created Logical Volume
* [x] Formatted Logical Volume
* [x] Mounted Logical Volume
* [x] Extended Logical Volume
* [x] Resized filesystem
* [x] Verified storage

**Status: Day 13 LVM practice completed.**
