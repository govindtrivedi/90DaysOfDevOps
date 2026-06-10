# Day 07 - Linux File System Hierarchy & Scenario-Based Practice

## Overview

Today I learned the Linux File System Hierarchy and practiced common troubleshooting scenarios that are frequently encountered in DevOps and System Administration.

## Topics Covered

### Linux File System Hierarchy

* `/` - Root directory
* `/home` - User home directories
* `/root` - Root user home directory
* `/etc` - Configuration files
* `/var/log` - Log files
* `/tmp` - Temporary files
* `/bin` - Essential binaries
* `/usr/bin` - User binaries
* `/opt` - Third-party applications

### Hands-On Practice

* Examined files and directories using `ls`
* Checked system configuration files
* Explored log locations
* Reviewed user home directory contents

## Scenario-Based Troubleshooting

### Scenario 1: Service Not Starting

* Checked service status
* Verified boot configuration
* Reviewed logs using journalctl
* Investigated startup failures

### Scenario 2: High CPU Usage

* Identified resource-intensive processes
* Analyzed CPU utilization
* Investigated process behavior

### Scenario 3: Finding Service Logs

* Located service logs using journalctl
* Viewed recent log entries
* Followed logs in real time

### Scenario 4: File Permission Issues

* Verified file permissions
* Added execute permissions
* Confirmed successful script execution

## Key Takeaway

Linux troubleshooting is a step-by-step process. Understanding where logs, configurations, binaries, and user files are located makes diagnosing issues much faster and more effective.

These skills form the foundation for real-world DevOps operations, incident response, and production support.
