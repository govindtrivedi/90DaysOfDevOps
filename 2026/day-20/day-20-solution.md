# Day 20 – Bash Scripting Challenge: Log Analyzer and Report Generator

## Objective

The goal of this project was to build a Bash script that analyzes a system log file and generates a daily summary report.

The script performs the following tasks:

* Validates the input log file.
* Counts lines containing `ERROR` or `Failed`.
* Finds `CRITICAL` events with line numbers.
* Finds the five most common `ERROR` messages.
* Generates a dated report.
* Optionally archives the processed log.

---

# Script: `log_analyzer.sh`

```bash
#!/bin/bash

set -euo pipefail

usage() {
    echo "Usage: $0 <log-file> [--archive]"
    exit 1
}

if [ "$#" -lt 1 ] || [ "$#" -gt 2 ]; then
    usage
fi

LOG_FILE="$1"
ARCHIVE=false

if [ "$#" -eq 2 ]; then
    if [ "$2" = "--archive" ]; then
        ARCHIVE=true
    else
        echo "Error: Unknown option: $2"
        usage
    fi
fi

if [ ! -f "$LOG_FILE" ]; then
    echo "Error: Log file does not exist: $LOG_FILE"
    exit 1
fi

if [ ! -r "$LOG_FILE" ]; then
    echo "Error: Log file is not readable: $LOG_FILE"
    exit 1
fi

ANALYSIS_DATE=$(date +%Y-%m-%d)
REPORT_FILE="log_report_${ANALYSIS_DATE}.txt"
LOG_BASENAME=$(basename "$LOG_FILE")

TOTAL_LINES=$(wc -l < "$LOG_FILE")

ERROR_COUNT=$(grep -E -c 'ERROR|Failed' "$LOG_FILE" || true)

CRITICAL_EVENTS=$(grep -n 'CRITICAL' "$LOG_FILE" || true)

TOP_ERRORS=$(
    grep 'ERROR' "$LOG_FILE" 2>/dev/null |
    sed 's/.*ERROR[[:space:]]*//' |
    sed 's/[[:space:]]*$//' |
    sort |
    uniq -c |
    sort -rn |
    head -5 || true
)

echo "========================================"
echo "        LOG ANALYZER SUMMARY"
echo "========================================"
echo "Date: $ANALYSIS_DATE"
echo "Log file: $LOG_FILE"
echo "Total lines: $TOTAL_LINES"
echo "Total errors: $ERROR_COUNT"
echo

echo "--- Critical Events ---"

if [ -n "$CRITICAL_EVENTS" ]; then
    while IFS= read -r event; do
        LINE_NUMBER="${event%%:*}"
        MESSAGE="${event#*:}"
        echo "Line $LINE_NUMBER: $MESSAGE"
    done <<< "$CRITICAL_EVENTS"
else
    echo "No critical events found."
fi

echo
echo "--- Top 5 Error Messages ---"

if [ -n "$TOP_ERRORS" ]; then
    while read -r count message; do
        printf "%-5s %s\n" "$count" "$message"
    done <<< "$TOP_ERRORS"
else
    echo "No ERROR messages found."
fi

{
    echo "========================================"
    echo "           DAILY LOG REPORT"
    echo "========================================"
    echo
    echo "Date of analysis: $ANALYSIS_DATE"
    echo "Log file: $LOG_FILE"
    echo "Total lines processed: $TOTAL_LINES"
    echo "Total error count: $ERROR_COUNT"
    echo

    echo "--- Top 5 Error Messages ---"

    if [ -n "$TOP_ERRORS" ]; then
        while read -r count message; do
            printf "%-5s %s\n" "$count" "$message"
        done <<< "$TOP_ERRORS"
    else
        echo "No ERROR messages found."
    fi

    echo
    echo "--- Critical Events ---"

    if [ -n "$CRITICAL_EVENTS" ]; then
        while IFS= read -r event; do
            LINE_NUMBER="${event%%:*}"
            MESSAGE="${event#*:}"
            echo "Line $LINE_NUMBER: $MESSAGE"
        done <<< "$CRITICAL_EVENTS"
    else
        echo "No critical events found."
    fi
} > "$REPORT_FILE"

echo
echo "Report generated: $REPORT_FILE"

if [ "$ARCHIVE" = true ]; then
    ARCHIVE_DIR="archive"
    mkdir -p "$ARCHIVE_DIR"

    mv "$LOG_FILE" "$ARCHIVE_DIR/"

    echo "Processed log moved to: $ARCHIVE_DIR/$LOG_BASENAME"
fi
```

---

# How the Script Works

## 1. Input Validation

The script requires at least one argument:

```bash
./log_analyzer.sh sample_log.log
```

If no argument is provided, it displays:

```text
Usage: ./log_analyzer.sh <log-file> [--archive]
```

It also checks that the supplied path is a regular file and that the file is readable.

---

## 2. Counting Errors

The following command counts lines containing either `ERROR` or `Failed`:

```bash
grep -E -c 'ERROR|Failed' "$LOG_FILE"
```

`grep -E` enables extended regular expressions, allowing the `|` operator to mean OR.

The `|| true` prevents `set -e` from terminating the script when `grep` finds no matches.

---

## 3. Finding Critical Events

Critical events are extracted using:

```bash
grep -n 'CRITICAL' "$LOG_FILE"
```

The `-n` option includes the line number.

The script then formats the result as:

```text
Line 84: 2026-08-19 10:15:23 CRITICAL Disk space below threshold
```

---

## 4. Finding Top Error Messages

The script first extracts lines containing `ERROR`:

```bash
grep 'ERROR' "$LOG_FILE"
```

Then it removes everything before the `ERROR` keyword:

```bash
sed 's/.*ERROR[[:space:]]*//'
```

The messages are sorted and counted:

```bash
sort | uniq -c | sort -rn | head -5
```

This produces the five most common error messages in descending order.

---

# Example Output

```text
========================================
        LOG ANALYZER SUMMARY
========================================
Date: 2026-08-19
Log file: sample_log.log
Total lines: 250
Total errors: 78

--- Critical Events ---
Line 84: 2026-08-19 10:15:23 CRITICAL Disk space below threshold
Line 217: 2026-08-19 14:32:01 CRITICAL Database connection lost

--- Top 5 Error Messages ---
45    Connection timed out
32    File not found
28    Permission denied
15    Disk I/O error
9     Out of memory

Report generated: log_report_2026-08-19.txt
```

The actual values depend on the supplied log file.

---

# Generated Report

The script generates a file using the current date:

```text
log_report_2026-08-19.txt
```

The report contains:

```text
========================================
           DAILY LOG REPORT
========================================

Date of analysis: 2026-08-19
Log file: sample_log.log
Total lines processed: 250
Total error count: 78

--- Top 5 Error Messages ---
45    Connection timed out
32    File not found
28    Permission denied
15    Disk I/O error
9     Out of memory

--- Critical Events ---
Line 84: 2026-08-19 10:15:23 CRITICAL Disk space below threshold
Line 217: 2026-08-19 14:32:01 CRITICAL Database connection lost
```

---

# Optional Archive Feature

The script supports:

```bash
./log_analyzer.sh sample_log.log --archive
```

This creates an `archive/` directory if necessary and moves the processed log into it.

Example:

```text
archive/
└── sample_log.log
```

A confirmation message is printed:

```text
Processed log moved to: archive/sample_log.log
```

---

# Commands and Tools Used

## `grep`

Used to search for specific patterns:

```bash
grep 'ERROR' logfile.log
grep -n 'CRITICAL' logfile.log
```

## `awk`

`awk` is useful for extracting and transforming fields from structured log data. In this implementation, `sed` is used to remove the timestamp/prefix before the `ERROR` message.

## `sed`

Used to remove everything before the `ERROR` keyword:

```bash
sed 's/.*ERROR[[:space:]]*//'
```

## `sort`

Used to put identical messages together:

```bash
sort
```

It is also used to sort counts numerically:

```bash
sort -rn
```

## `uniq`

Used with `-c` to count duplicate messages:

```bash
uniq -c
```

## `head`

Used to select the top five results:

```bash
head -5
```

## `wc`

Used to count the total number of lines:

```bash
wc -l
```

## `find`

Not required for the main analysis, but useful in the optional archive/maintenance workflows from previous exercises.

---

# What I Learned

## 1. Text-processing tools are powerful

Commands such as `grep`, `sed`, `sort`, `uniq`, and `head` can be combined into pipelines to process large amounts of log data efficiently.

## 2. Exit codes matter

With:

```bash
set -euo pipefail
```

commands that fail unexpectedly can terminate the script. Commands such as `grep` can legitimately return a non-zero status when they find no matches, so `|| true` can be used where that situation is expected.

## 3. Automation turns raw logs into useful information

Instead of manually searching a large log file, the script automatically produces:

* Total lines processed
* Error count
* Most common errors
* Critical events
* A dated report

This is a practical foundation for DevOps monitoring and log-analysis automation.

---

# Final Directory Structure

```text
2026/
└── day-20/
    ├── log_analyzer.sh
    ├── sample_log.log
    ├── log_report_2026-08-19.txt
    └── day-20-solution.md
```

The generated report date will change depending on when the script is executed.

---

# Commands Used

Make the script executable:

```bash
chmod +x log_analyzer.sh
```

Run the analyzer:

```bash
./log_analyzer.sh sample_log.log
```

Run with optional archiving:

```bash
./log_analyzer.sh sample_log.log --archive
```

Inspect the generated report:

```bash
cat log_report_$(date +%Y-%m-%d).txt
```

Git submission:

```bash
git add 2026/day-20/
git commit -m "Complete Day 20 log analyzer project"
git push
```

## Day 20 Summary

Day 20 combined Bash scripting with practical log analysis. I learned how to validate command-line input, process logs with standard Linux text-processing utilities, calculate common error messages, identify critical events, and generate automated daily reports.
