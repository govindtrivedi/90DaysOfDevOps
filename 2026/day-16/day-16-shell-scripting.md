# Day 16 — Shell Scripting

## Task 1: First Script

### `hello.sh`

```bash
#!/bin/bash

echo "Hello, DevOps!"
```

### Run

```bash
chmod +x hello.sh
./hello.sh
```

### Output

```text
Hello, DevOps!
```

### What happens if the shebang is removed?

The shebang tells the operating system which interpreter should execute the script. If the script is run explicitly with `bash hello.sh`, Bash is selected directly and the script will normally work. When running `./hello.sh`, however, the interpreter is no longer explicitly specified, so behavior can vary by environment.

---

## Task 2: Variables

### `variables.sh`

```bash
#!/bin/bash

NAME="Shubham"
ROLE="DevOps Engineer"

echo "Hello, I am $NAME and I am a $ROLE"
```

### Output

```text
Hello, I am Shubham and I am a DevOps Engineer
```

### Single Quotes vs Double Quotes

```bash
NAME="Shubham"

echo 'Hello, $NAME'
echo "Hello, $NAME"
```

Output:

```text
Hello, $NAME
Hello, Shubham
```

Single quotes treat the contents literally, while double quotes allow variables to be expanded.

---

## Task 3: User Input

### `greet.sh`

```bash
#!/bin/bash

read -p "Enter your name: " NAME
read -p "Enter your favourite tool: " TOOL

echo "Hello $NAME, your favourite tool is $TOOL"
```

### Sample Output

```text
Enter your name: Shubham
Enter your favourite tool: Docker
Hello Shubham, your favourite tool is Docker
```

The `read` command accepts input from the user and stores it in a variable.

---

## Task 4: If-Else Conditions

### `check_number.sh`

```bash
#!/bin/bash

read -p "Enter a number: " NUMBER

if [ "$NUMBER" -gt 0 ]; then
    echo "The number is positive."
elif [ "$NUMBER" -lt 0 ]; then
    echo "The number is negative."
else
    echo "The number is zero."
fi
```

### Sample Output

```text
Enter a number: 10
The number is positive.
```

---

### `file_check.sh`

```bash
#!/bin/bash

read -p "Enter a filename: " FILE

if [ -f "$FILE" ]; then
    echo "File exists: $FILE"
else
    echo "File does not exist: $FILE"
fi
```

### Sample Output

```text
Enter a filename: hello.sh
File exists: hello.sh
```

The `-f` condition checks whether the specified path exists and is a regular file.

---

## Task 5: Server Check

### `server_check.sh`

```bash
#!/bin/bash

SERVICE="nginx"

read -p "Do you want to check the status? (y/n): " CHOICE

if [ "$CHOICE" = "y" ]; then
    systemctl status "$SERVICE"

    if systemctl is-active --quiet "$SERVICE"; then
        echo "$SERVICE is active."
    else
        echo "$SERVICE is not active."
    fi
elif [ "$CHOICE" = "n" ]; then
    echo "Skipped."
else
    echo "Invalid choice."
fi
```

### Sample Output

```text
Do you want to check the status? (y/n): y
...
nginx is active.
```

When the user enters `n`:

```text
Do you want to check the status? (y/n): n
Skipped.
```

---

## What I Learned

1. **Shell scripts use an interpreter:** The shebang specifies which interpreter should execute the script.

2. **Variables and user input:** Variables store values, while `read` allows a script to accept input from the user. Double quotes allow variable expansion.

3. **Conditions enable automation:** `if`, `elif`, `else`, and tests such as `-f` can be used to make decisions based on files, numbers, and system state.

## Commands Used

```bash
chmod +x hello.sh variables.sh greet.sh check_number.sh file_check.sh server_check.sh
```

```bash
./hello.sh
./variables.sh
./greet.sh
./check_number.sh
./file_check.sh
./server_check.sh
```

