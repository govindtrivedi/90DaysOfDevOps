# Day 19 – Shell Scripting Project: Log Rotation, Backup & Crontab

## Introduction

Day 19 focused on applying shell scripting concepts from Days 16–18 to real-world DevOps tasks.

The projects covered:

* Log rotation
* Server backups
* Cron scheduling
* Health checks
* Combining multiple functions into a maintenance script
* Error handling and strict mode

---

# Task 1: Log Rotation

## `log_rotate.sh`

```bash
#!/bin/bash

set -euo pipefail

if [ "$#" -ne 1 ]; then
    echo "Usage: $0 <log-directory>"
    exit 1
fi

LOG_DIR="$1"

if [ ! -d "$LOG_DIR" ]; then
    echo "Error: Directory does not exist: $LOG_DIR"
    exit 1
fi

compressed_count=0
deleted_count=0

echo "Starting log rotation for: $LOG_DIR"

while IFS= read -r -d '' file; do
    echo "Compressing: $file"
    gzip "$file"
    ((compressed_count+=1))
done < <(find "$LOG_DIR" -type f -name "*.log" -mtime +7 -print0)

while IFS= read -r -d '' file; do
    echo "Deleting old archive: $file"
    rm -f "$file"
    ((deleted_count+=1))
done < <(find "$LOG_DIR" -type f -name "*.gz" -mtime +30 -print0)

echo "Log rotation completed."
echo "Files compressed: $compressed_count"
echo "Archives deleted: $deleted_count"
```

## How it works

The script:

1. Accepts a log directory as `$1`.
2. Checks whether the directory exists.
3. Finds `.log` files older than 7 days.
4. Compresses them with `gzip`.
5. Finds `.gz` files older than 30 days.
6. Deletes old compressed logs.
7. Reports the number of compressed and deleted files.

## Sample Output

```text
Starting log rotation for: /var/log/myapp
Compressing: /var/log/myapp/app-2026-01-20.log
Compressing: /var/log/myapp/app-2026-01-21.log
Deleting old archive: /var/log/myapp/app-2025-12-01.log.gz
Log rotation completed.
Files compressed: 2
Archives deleted: 1
```

---

# Task 2: Server Backup

## `backup.sh`

```bash
#!/bin/bash

set -euo pipefail

if [ "$#" -ne 2 ]; then
    echo "Usage: $0 <source-directory> <backup-destination>"
    exit 1
fi

SOURCE_DIR="$1"
BACKUP_DEST="$2"
TIMESTAMP=$(date +%Y-%m-%d_%H-%M-%S)
ARCHIVE_NAME="backup-${TIMESTAMP}.tar.gz"
ARCHIVE_PATH="${BACKUP_DEST}/${ARCHIVE_NAME}"

if [ ! -d "$SOURCE_DIR" ]; then
    echo "Error: Source directory does not exist: $SOURCE_DIR"
    exit 1
fi

if [ ! -d "$BACKUP_DEST" ]; then
    echo "Backup destination does not exist. Creating it..."
    mkdir -p "$BACKUP_DEST"
fi

echo "Creating backup..."
echo "Source: $SOURCE_DIR"
echo "Destination: $ARCHIVE_PATH"

tar -czf "$ARCHIVE_PATH" \
    -C "$(dirname "$SOURCE_DIR")" \
    "$(basename "$SOURCE_DIR")"

if [ ! -f "$ARCHIVE_PATH" ]; then
    echo "Error: Backup archive was not created."
    exit 1
fi

ARCHIVE_SIZE=$(du -h "$ARCHIVE_PATH" | cut -f1)

echo "Backup created successfully."
echo "Archive: $ARCHIVE_PATH"
echo "Size: $ARCHIVE_SIZE"

echo "Removing backups older than 14 days..."

find "$BACKUP_DEST" \
    -type f \
    -name "backup-*.tar.gz" \
    -mtime +14 \
    -delete

echo "Old backups cleaned up."
```

## Sample Output

```text
Creating backup...
Source: /home/user/myapp
Destination: /home/user/backups/backup-2026-08-19_22-00-00.tar.gz
Backup created successfully.
Archive: /home/user/backups/backup-2026-08-19_22-00-00.tar.gz
Size: 18M
Removing backups older than 14 days...
Old backups cleaned up.
```

The backup script uses `tar` and `gzip` to create a compressed archive.

It also removes backup archives older than 14 days.

---

# Task 3: Crontab

## Checking Existing Cron Jobs

I can check the current user's cron jobs with:

```bash
crontab -l
```

If no cron jobs are configured, the system may show:

```text
no crontab for username
```

## Cron Syntax

```text
* * * * * command
│ │ │ │ │
│ │ │ │ └── Day of week (0-7)
│ │ │ └──── Month (1-12)
│ │ └────── Day of month (1-31)
│ └──────── Hour (0-23)
└────────── Minute (0-59)
```

## Log Rotation — Every Day at 2 AM

```cron
0 2 * * * /home/user/2026/day-19/log_rotate.sh /var/log/myapp >> /var/log/log_rotate.log 2>&1
```

## Backup — Every Sunday at 3 AM

```cron
0 3 * * 0 /home/user/2026/day-19/backup.sh /home/user/myapp /home/user/backups >> /var/log/backup.log 2>&1
```

## Health Check — Every 5 Minutes

```cron
*/5 * * * * /home/user/2026/day-19/health_check.sh >> /var/log/health_check.log 2>&1
```

These entries are documented for the exercise and should only be added after verifying that the paths and permissions match the actual environment.

---

# Health Check Script

## `health_check.sh`

```bash
#!/bin/bash

set -euo pipefail

HOSTNAME=$(hostname)
UPTIME=$(uptime -p)
DISK_USAGE=$(df -h / | awk 'NR==2 {print $5}')

echo "[$(date '+%Y-%m-%d %H:%M:%S')] Health Check"
echo "Hostname: $HOSTNAME"
echo "Uptime: $UPTIME"
echo "Root disk usage: $DISK_USAGE"

DISK_PERCENT=${DISK_USAGE%\%}

if [ "$DISK_PERCENT" -ge 90 ]; then
    echo "WARNING: Disk usage is above 90%."
    exit 1
fi

echo "Status: OK"
```

## Sample Output

```text
[2026-08-19 22:05:00] Health Check
Hostname: devops-server
Uptime: up 3 hours, 22 minutes
Root disk usage: 42%
Status: OK
```

---

# Task 4: Scheduled Maintenance

## `maintenance.sh`

```bash
#!/bin/bash

set -euo pipefail

LOG_DIR="/var/log/myapp"
SOURCE_DIR="/home/user/myapp"
BACKUP_DEST="/home/user/backups"
MAINTENANCE_LOG="/var/log/maintenance.log"

log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*" >> "$MAINTENANCE_LOG"
}

rotate_logs() {
    local compressed_count=0
    local deleted_count=0

    log "Starting log rotation."

    if [ ! -d "$LOG_DIR" ]; then
        log "ERROR: Log directory does not exist: $LOG_DIR"
        return 1
    fi

    while IFS= read -r -d '' file; do
        log "Compressing: $file"
        gzip "$file"
        ((compressed_count+=1))
    done < <(find "$LOG_DIR" -type f -name "*.log" -mtime +7 -print0)

    while IFS= read -r -d '' file; do
        log "Deleting: $file"
        rm -f "$file"
        ((deleted_count+=1))
    done < <(find "$LOG_DIR" -type f -name "*.gz" -mtime +30 -print0)

    log "Log rotation complete. Compressed: $compressed_count, Deleted: $deleted_count."
}

create_backup() {
    local timestamp
    local archive_name
    local archive_path
    local archive_size

    log "Starting backup."

    if [ ! -d "$SOURCE_DIR" ]; then
        log "ERROR: Source directory does not exist: $SOURCE_DIR"
        return 1
    fi

    mkdir -p "$BACKUP_DEST"

    timestamp=$(date +%Y-%m-%d_%H-%M-%S)
    archive_name="backup-${timestamp}.tar.gz"
    archive_path="${BACKUP_DEST}/${archive_name}"

    tar -czf "$archive_path" \
        -C "$(dirname "$SOURCE_DIR")" \
        "$(basename "$SOURCE_DIR")"

    if [ ! -f "$archive_path" ]; then
        log "ERROR: Backup creation failed."
        return 1
    fi

    archive_size=$(du -h "$archive_path" | cut -f1)

    log "Backup created: $archive_path"
    log "Backup size: $archive_size"

    find "$BACKUP_DEST" \
        -type f \
        -name "backup-*.tar.gz" \
        -mtime +14 \
        -delete

    log "Old backups cleaned up."
}

main() {
    log "========== Maintenance started =========="

    if rotate_logs; then
        log "Log rotation completed successfully."
    else
        log "Log rotation failed."
    fi

    if create_backup; then
        log "Backup completed successfully."
    else
        log "Backup failed."
    fi

    log "========== Maintenance finished =========="
}

main
```

## Daily Maintenance Cron Entry

```cron
0 1 * * * /home/user/2026/day-19/maintenance.sh
```

This runs the maintenance script every day at 1 AM.

The script combines log rotation and backups and writes timestamped messages to:

```text
/var/log/maintenance.log
```

---

# What I Learned

## 1. Automation with Cron

Cron allows repetitive administrative tasks such as backups, health checks, and log rotation to run automatically according to a schedule.

## 2. Backup and Log Management

Logs can consume significant disk space. Compressing older logs and deleting very old archives helps control disk usage.

Creating timestamped backups provides a simple way to maintain multiple backup versions.

## 3. Reliable Shell Automation

Using:

```bash
set -euo pipefail
```

along with argument validation, directory checks, exit codes, functions, and timestamped logging makes automation scripts safer and easier to troubleshoot.

---

# Final Directory Structure

```text
2026/
└── day-19/
    ├── log_rotate.sh
    ├── backup.sh
    ├── health_check.sh
    ├── maintenance.sh
    └── day-19-project.md
```

# Commands Used

Make scripts executable:

```bash
chmod +x log_rotate.sh backup.sh health_check.sh maintenance.sh
```

Test the scripts:

```bash
./log_rotate.sh /tmp/myapp-logs
./backup.sh /home/user/myapp /home/user/backups
./health_check.sh
./maintenance.sh
```

Check cron:

```bash
crontab -l
```

Edit cron:

```bash
crontab -e
```

Git submission:

```bash
git add 2026/day-19/
git commit -m "Complete Day 19 shell scripting project"
git push
```

## Day 19 Summary

Day 19 connected shell scripting with practical DevOps operations. I created scripts for log rotation, backups, health monitoring, and scheduled maintenance. I also learned how cron can automate these tasks and how proper error handling and logging make scheduled scripts easier to operate and troubleshoot.
