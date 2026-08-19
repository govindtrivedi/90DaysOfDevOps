# Day 17 – Shell Scripting: Loops, Arguments & Error Handling

## Task 1: For Loop

### `for_loop.sh`

```bash
#!/bin/bash

FRUITS=("Apple" "Banana" "Mango" "Orange" "Grapes")

for fruit in "${FRUITS[@]}"; do
    echo "$fruit"
done
```

### Output

```text
Apple
Banana
Mango
Orange
Grapes
```

### `count.sh`

```bash
#!/bin/bash

for number in {1..10}; do
    echo "$number"
done
```

### Output

```text
1
2
3
4
5
6
7
8
9
10
```

The `for` loop is useful when we need to repeat an operation for every item in a list or a known range of values.

---

## Task 2: While Loop

### `countdown.sh`

```bash
#!/bin/bash

read -p "Enter a number: " NUMBER

while [ "$NUMBER" -ge 0 ]; do
    echo "$NUMBER"
    NUMBER=$((NUMBER - 1))
done

echo "Done!"
```

### Output

```text
Enter a number: 5
5
4
3
2
1
0
Done!
```

A `while` loop continues executing as long as its condition is true.

---

## Task 3: Command-Line Arguments

### `greet.sh`

```bash
#!/bin/bash

if [ "$#" -eq 0 ]; then
    echo "Usage: ./greet.sh <name>"
    exit 1
fi

echo "Hello, $1!"
```

### Output

```text
$ ./greet.sh Shubham
Hello, Shubham!
```

If no argument is supplied:

```text
$ ./greet.sh
Usage: ./greet.sh <name>
```

### `args_demo.sh`

```bash
#!/bin/bash

echo "Total number of arguments: $#"
echo "All arguments: $@"
echo "Script name: $0"
```

### Output

```text
$ ./args_demo.sh DevOps Docker AWS
Total number of arguments: 3
All arguments: DevOps Docker AWS
Script name: ./args_demo.sh
```

Important argument variables:

* `$0` — name/path used to execute the script
* `$1` — first argument
* `$2` — second argument
* `$#` — number of arguments
* `$@` — all arguments

---

## Task 4: Install Packages via Script

### `install_packages.sh`

```bash
#!/bin/bash

if [ "$EUID" -ne 0 ]; then
    echo "Run as root. Example: sudo ./install_packages.sh"
    exit 1
fi

PACKAGES=("nginx" "curl" "wget")

if command -v dpkg >/dev/null 2>&1; then

    for package in "${PACKAGES[@]}"; do
        if dpkg -s "$package" &>/dev/null; then
            echo "$package is already installed. Skipping."
        else
            echo "$package is not installed. Installing..."
            apt-get update
            apt-get install -y "$package"

            if [ $? -eq 0 ]; then
                echo "$package installed successfully."
            else
                echo "Failed to install $package."
            fi
        fi
    done

elif command -v rpm >/dev/null 2>&1; then

    for package in "${PACKAGES[@]}"; do
        if rpm -q "$package" &>/dev/null; then
            echo "$package is already installed. Skipping."
        else
            echo "$package is not installed. Installing..."
            dnf install -y "$package"

            if [ $? -eq 0 ]; then
                echo "$package installed successfully."
            else
                echo "Failed to install $package."
            fi
        fi
    done

else
    echo "Unsupported package manager."
    exit 1
fi
```

### Example Output

```text
nginx is not installed. Installing...
nginx installed successfully.
curl is already installed. Skipping.
wget is already installed. Skipping.
```

The script checks whether it is running as root before attempting package installation.

Run it with:

```bash
sudo ./install_packages.sh
```

---

## Task 5: Error Handling

### `safe_script.sh`

```bash
#!/bin/bash

set -e

mkdir /tmp/devops-test || echo "Directory already exists"

cd /tmp/devops-test || {
    echo "Failed to navigate to /tmp/devops-test"
    exit 1
}

touch test-file.txt || {
    echo "Failed to create test-file.txt"
    exit 1
}

echo "Directory created/accessed successfully."
echo "File created successfully."
```

### Output

```text
Directory created/accessed successfully.
File created successfully.
```

If the directory already exists:

```text
mkdir: cannot create directory ‘/tmp/devops-test’: File exists
Directory already exists
Directory created/accessed successfully.
File created successfully.
```

`set -e` makes the script exit when a command fails, while `||` can be used to handle an expected failure or provide an error message.

---

## What I Learned

1. **Loops automate repetitive tasks:** `for` loops are useful for lists and ranges, while `while` loops are useful when execution depends on a condition.

2. **Command-line arguments make scripts reusable:** `$1`, `$#`, `$@`, and `$0` allow a script to receive and process information from the command line.

3. **Error handling makes scripts safer:** `set -e`, exit codes, `if` conditions, and `||` can prevent scripts from silently continuing after important failures.

## Commands Used

```bash
chmod +x for_loop.sh count.sh countdown.sh greet.sh args_demo.sh install_packages.sh safe_script.sh
```

```bash
./for_loop.sh
./count.sh
./countdown.sh
./greet.sh Shubham
./args_demo.sh DevOps Docker AWS
sudo ./install_packages.sh
./safe_script.sh
```
