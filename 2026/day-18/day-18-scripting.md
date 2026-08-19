# Day 18 – Shell Scripting: Functions & Intermediate Concepts

## Task 1: Basic Functions

### `functions.sh`

```bash
#!/bin/bash

greet() {
    local name="$1"
    echo "Hello, $name!"
}

add() {
    local num1="$1"
    local num2="$2"
    echo "Sum: $((num1 + num2))"
}

greet "Shubham"
add 10 20
```

### Output

```text
Hello, Shubham!
Sum: 30
```

The `greet` function accepts a name as its first argument. The `add` function accepts two numbers and prints their sum.

Functions make scripts more reusable because a block of commands can be defined once and called multiple times.

---

## Task 2: Functions with System Information

### `disk_check.sh`

```bash
#!/bin/bash

check_disk() {
    echo "=== Disk Usage ==="
    df -h /
}

check_memory() {
    echo
    echo "=== Memory Usage ==="
    free -h
}

main() {
    check_disk
    check_memory
}

main
```

### Output

```text
=== Disk Usage ===
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        50G   20G   28G  42% /

=== Memory Usage ===
               total        used        free
Mem:            7.7Gi       3.1Gi       1.2Gi
Swap:           2.0Gi          0B       2.0Gi
```

The exact disk and memory values depend on the machine running the script.

---

## Task 3: Strict Mode

### `strict_demo.sh`

```bash
#!/bin/bash

set -euo pipefail

demo_unset_variable() {
    echo "=== set -u demonstration ==="

    if ! ( set -u; echo "$UNDEFINED_VARIABLE" ); then
        echo "set -u stopped the command because the variable is undefined."
    fi
}

demo_failed_command() {
    echo
    echo "=== set -e demonstration ==="

    if ! ( set -e; false; echo "This will not execute" ); then
        echo "set -e stopped the failing command."
    fi
}

demo_pipefail() {
    echo
    echo "=== pipefail demonstration ==="

    if ! ( set -o pipefail; false | true ); then
        echo "pipefail detected that a command in the pipeline failed."
    fi
}

demo_unset_variable
demo_failed_command
demo_pipefail

echo
echo "Strict mode demonstration completed."
```

### Output

```text
=== set -u demonstration ===
strict_demo.sh: line ...: UNDEFINED_VARIABLE: unbound variable
set -u stopped the command because the variable is undefined.

=== set -e demonstration ===
set -e stopped the failing command.

=== pipefail demonstration ===
pipefail detected that a command in the pipeline failed.

Strict mode demonstration completed.
```

### Explanation of `set -euo pipefail`

#### `set -e`

Stops execution when a command fails, subject to Bash's exceptions for contexts such as conditional tests and some lists.

#### `set -u`

Treats an unset variable as an error when the script tries to expand it.

For example:

```bash
echo "$UNDEFINED_VARIABLE"
```

can produce an `unbound variable` error when `set -u` is enabled.

#### `set -o pipefail`

Normally, a pipeline's exit status is the status of its last command.

For example:

```bash
false | true
```

normally has the status of `true`.

With `pipefail`, the pipeline fails because `false` failed.

### Combined Strict Mode

```bash
set -euo pipefail
```

provides three useful safety mechanisms:

* Exit on unexpected command failures.
* Detect accidental use of unset variables.
* Detect failures inside pipelines.

---

## Task 4: Local Variables

### `local_demo.sh`

```bash
#!/bin/bash

GLOBAL_VAR="Global value"

local_example() {
    local LOCAL_VAR="Local value"
    echo "Inside function:"
    echo "LOCAL_VAR = $LOCAL_VAR"
    echo "GLOBAL_VAR = $GLOBAL_VAR"
}

regular_example() {
    REGULAR_VAR="Regular variable"
    echo "Inside function:"
    echo "REGULAR_VAR = $REGULAR_VAR"
}

echo "Before functions:"
echo "GLOBAL_VAR = $GLOBAL_VAR"

local_example

echo
echo "Outside local_example:"
if [ -z "${LOCAL_VAR+x}" ]; then
    echo "LOCAL_VAR does not exist outside the function."
else
    echo "LOCAL_VAR = $LOCAL_VAR"
fi

regular_example

echo
echo "Outside regular_example:"
echo "REGULAR_VAR = $REGULAR_VAR"
```

### Output

```text
Before functions:
GLOBAL_VAR = Global value

Inside function:
LOCAL_VAR = Local value
GLOBAL_VAR = Global value

Outside local_example:
LOCAL_VAR does not exist outside the function.

Inside function:
REGULAR_VAR = Regular variable

Outside regular_example:
REGULAR_VAR = Regular variable
```

The `local` keyword restricts a variable to the function where it is declared. A regular variable can remain available after the function finishes.

Using `local` helps prevent functions from accidentally modifying variables used elsewhere in a script.

---

## Task 5: System Information Reporter

### `system_info.sh`

```bash
#!/bin/bash

set -euo pipefail

print_hostname_os() {
    echo "=== Hostname & OS ==="

    echo "Hostname: $(hostname)"

    if [ -f /etc/os-release ]; then
        . /etc/os-release
        echo "OS: ${PRETTY_NAME:-Unknown}"
    else
        echo "OS: Unknown"
    fi
}

print_uptime() {
    echo
    echo "=== Uptime ==="
    uptime
}

print_disk_usage() {
    echo
    echo "=== Top 5 Disk Usage ==="

    du -x -h / 2>/dev/null |
        sort -hr |
        head -n 5 || true
}

print_memory_usage() {
    echo
    echo "=== Memory Usage ==="
    free -h
}

print_top_processes() {
    echo
    echo "=== Top 5 CPU-Consuming Processes ==="

    ps -eo pid,comm,%cpu,%mem --sort=-%cpu |
        head -n 6
}

main() {
    print_hostname_os
    print_uptime
    print_disk_usage
    print_memory_usage
    print_top_processes
}

main
```

### Example Output

```text
=== Hostname & OS ===
Hostname: devops-machine
OS: Ubuntu 24.04 LTS

=== Uptime ===
 21:45:10 up 2 days, 4:32, 1 user, load average: 0.42, 0.35, 0.31

=== Top 5 Disk Usage ===
12G     /
5.2G    /usr
3.1G    /var
1.8G    /home
850M    /opt

=== Memory Usage ===
               total        used        free
Mem:           15Gi        6.2Gi       4.1Gi
Swap:          2.0Gi          0B       2.0Gi

=== Top 5 CPU-Consuming Processes ===
    PID COMMAND         %CPU %MEM
   1234 firefox         18.4  8.2
   2345 docker           7.1  3.5
    987 systemd          2.1  0.4
   3456 node             1.8  2.1
    456 sshd             0.5  0.2
```

The values will differ depending on the system.

---

## What I Learned

### 1. Functions make scripts reusable

Functions allow related commands to be grouped into reusable blocks. Arguments can be passed to functions using `$1`, `$2`, and other positional parameters.

### 2. Strict mode makes scripts safer

Using:

```bash
set -euo pipefail
```

helps detect command failures, unset variables, and failures inside pipelines.

### 3. Local variables prevent unintended side effects

Using `local` inside functions keeps temporary variables scoped to that function. This makes larger scripts easier to maintain and reduces accidental variable conflicts.

---

## Commands Used

Make the scripts executable:

```bash
chmod +x functions.sh disk_check.sh strict_demo.sh local_demo.sh system_info.sh
```

Run the scripts:

```bash
./functions.sh
./disk_check.sh
./strict_demo.sh
./local_demo.sh
./system_info.sh
```

---

## Submission Structure

The Day 18 directory should contain:

```text
2026/
└── day-18/
    ├── functions.sh
    ├── disk_check.sh
    ├── strict_demo.sh
    ├── local_demo.sh
    ├── system_info.sh
    └── day-18-scripting.md
```

After testing the scripts:

```bash
cd 2026/day-18

git add .

git commit -m "Complete Day 18 shell scripting"

git push
```

## Day 18 Summary

Today I progressed from basic shell scripts to more maintainable scripts by using functions, local variables, strict mode, command pipelines, and system-information commands. These concepts are useful for writing reliable automation scripts in real DevOps environments.
