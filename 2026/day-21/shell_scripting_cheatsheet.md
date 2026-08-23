Bash Scripting & DevOps Cheat Sheet

A practical quick-reference covering scripting fundamentals, conditionals, loops, functions, text processing, one-liners, and error handling.

Quick Reference
Topic	Key Syntax	Example
Shebang	#!/bin/bash	#!/bin/bash
Variable	VAR="value"	NAME="DevOps"
Argument	$1, $2	./script.sh arg1
Exit status	$?	echo $?
If	if [ condition ]; then	if [ -f file ]; then
For loop	for i in list; do	for i in 1 2 3; do
While loop	while condition; do	while [ "$n" -lt 5 ]; do
Function	name() { ... }	greet() { echo "Hi"; }
Case	case "$x" in	case "$x" in start)
Grep	grep pattern file	grep -in "error" app.log
Awk	awk '{print $1}' file	awk -F: '{print $1}' /etc/passwd
Sed	sed 's/old/new/g' file	sed -i 's/foo/bar/g' config.txt
Cut	cut -d: -f1 file	cut -d, -f2 users.csv
Sort	sort	sort -n numbers.txt
Uniq	uniq -c	sort names.txt | uniq -c
Head/Tail	head, tail	tail -f app.log
Error handling	set -euo pipefail	set -euo pipefail
Task 1: Bash Script Basics
1. Shebang

The shebang tells the operating system which interpreter should execute the script.

#!/bin/bash

echo "Hello DevOps"

Why it matters:

./script.sh

uses the interpreter specified by the shebang.

2. Running a Script
Make executable
chmod +x script.sh
Execute directly
./script.sh
Run through Bash
bash script.sh

chmod +x is needed for direct execution but not when explicitly invoking bash.

3. Comments
Single-line comment
# This is a comment
echo "Hello"
Inline comment
NAME="Govind"  # Store the user's name

Comments document scripts without being executed.

4. Variables
Declare
NAME="Govind"
ENVIRONMENT="production"
Use
echo $NAME
echo "$ENVIRONMENT"
Quoting matters
VAR="Hello World"

echo $VAR
echo "$VAR"
echo '$VAR'

Output:

Hello World
Hello World
$VAR

Prefer "$VAR" when expanding variables because it preserves spaces and prevents unwanted word splitting.

5. Reading User Input

Use read to accept input from the terminal.

read -p "Enter your name: " NAME
echo "Hello, $NAME"

Example:

Enter your name: Govind
Hello, Govind
6. Command-Line Arguments

Example:

#!/bin/bash

echo "Script: $0"
echo "First argument: $1"
echo "Number of arguments: $#"
echo "All arguments: $@"
echo "Previous command status: $?"

Run:

./script.sh AWS Kubernetes Docker

Common parameters:

Parameter	Meaning
$0	Script name
$1	First argument
$2	Second argument
$#	Number of arguments
$@	All arguments
$?	Exit status of previous command

Important: $? changes after every command, so capture it immediately when needed.

Task 2: Operators and Conditionals
1. String Comparisons
A="DevOps"
B="Cloud"

[ "$A" = "$B" ]
[ "$A" != "$B" ]

Empty/non-empty tests:

[ -z "$A" ]   # Empty
[ -n "$A" ]   # Not empty

Example:

if [ -z "$NAME" ]; then
    echo "Name is empty"
fi
2. Integer Comparisons
A=10
B=20

[ "$A" -eq "$B" ]  # Equal
[ "$A" -ne "$B" ]  # Not equal
[ "$A" -lt "$B" ]  # Less than
[ "$A" -gt "$B" ]  # Greater than
[ "$A" -le "$B" ]  # Less than or equal
[ "$A" -ge "$B" ]  # Greater than or equal

Example:

if [ "$A" -lt "$B" ]; then
    echo "$A is smaller"
fi
3. File Test Operators
[ -f "$FILE" ]  # Regular file
[ -d "$DIR" ]   # Directory
[ -e "$PATH" ]  # Exists
[ -r "$FILE" ]  # Readable
[ -w "$FILE" ]  # Writable
[ -x "$FILE" ]  # Executable
[ -s "$FILE" ]  # Not empty

Example:

if [ -f "/var/log/app.log" ]; then
    echo "Log file exists"
fi
4. if, elif, else
STATUS=200

if [ "$STATUS" -eq 200 ]; then
    echo "Success"
elif [ "$STATUS" -eq 404 ]; then
    echo "Not found"
else
    echo "Other status"
fi
5. Logical Operators
AND
if [ "$USER" = "root" ] && [ -f "/etc/passwd" ]; then
    echo "Condition matched"
fi
OR
if [ "$ENV" = "dev" ] || [ "$ENV" = "test" ]; then
    echo "Non-production environment"
fi
NOT
if ! [ -f "$FILE" ]; then
    echo "File does not exist"
fi
6. Case Statements

Useful when handling multiple fixed choices.

ACTION="$1"

case "$ACTION" in
    start)
        echo "Starting application"
        ;;
    stop)
        echo "Stopping application"
        ;;
    restart)
        echo "Restarting application"
        ;;
    *)
        echo "Usage: $0 {start|stop|restart}"
        ;;
esac

case ends with esac.

Task 3: Loops
1. for Loop — List Based
for env in dev test prod; do
    echo "Environment: $env"
done
C-style
for ((i=1; i<=5; i++)); do
    echo "$i"
done
2. while Loop

Runs while the condition is true.

COUNT=1

while [ "$COUNT" -le 5 ]; do
    echo "$COUNT"
    ((COUNT++))
done
3. until Loop

Runs until the condition becomes true.

COUNT=1

until [ "$COUNT" -gt 5 ]; do
    echo "$COUNT"
    ((COUNT++))
done
4. break and continue
break

Stops the loop.

for i in 1 2 3 4 5; do
    if [ "$i" -eq 3 ]; then
        break
    fi
    echo "$i"
done
continue

Skips the current iteration.

for i in 1 2 3 4 5; do
    if [ "$i" -eq 3 ]; then
        continue
    fi
    echo "$i"
done
5. Looping Over Files
for file in *.log; do
    echo "Processing: $file"
done

Useful for processing log files in automation scripts.

6. Looping Over Command Output
while read -r line; do
    echo "Line: $line"
done < servers.txt

For command output:

printf '%s\n' "server1" "server2" | while read -r line; do
    echo "Server: $line"
done

Use read -r to prevent backslashes from being interpreted.

Task 4: Functions
1. Define a Function
greet() {
    echo "Hello DevOps"
}
2. Call a Function
greet

Output:

Hello DevOps
3. Function Arguments

Arguments are available as $1, $2, etc. inside the function.

greet() {
    echo "Hello $1"
}

greet "Govind"

Output:

Hello Govind

Multiple arguments:

deploy() {
    echo "Application: $1"
    echo "Environment: $2"
}

deploy "banking-app" "production"
4. return vs echo
return

Returns an exit status from a function.

check_file() {
    [ -f "$1" ]
    return $?
}

check_file "app.log"

if [ "$?" -eq 0 ]; then
    echo "File exists"
fi
echo

Outputs data that can be captured.

get_environment() {
    echo "production"
}

ENV=$(get_environment)
echo "$ENV"

Use return for success/failure status and echo when you need to produce a value.

5. Local Variables

Use local to keep variables scoped to the function.

deploy() {
    local ENV="$1"
    echo "Deploying to $ENV"
}

deploy "production"
Task 5: Text Processing Commands
1. grep

Search text for patterns.

grep "ERROR" app.log

Useful options:

grep -i "error" app.log       # Case-insensitive
grep -r "ERROR" ./logs        # Recursive
grep -c "ERROR" app.log       # Count matches
grep -n "ERROR" app.log       # Line numbers
grep -v "INFO" app.log        # Invert match
grep -E "ERROR|WARN" app.log  # Extended regex

DevOps example:

grep -in "error" /var/log/app.log
2. awk

Excellent for columns and structured text.

Print column
awk '{print $1}' users.txt
Field separator
awk -F: '{print $1}' /etc/passwd
Pattern
awk '$3 > 80 {print $1, $3}' servers.txt
BEGIN / END
awk 'BEGIN {print "Users"} {print $1} END {print "Done"}' users.txt
3. sed
Substitute
sed 's/old/new/g' file.txt
Delete lines
sed '/DEBUG/d' app.log
In-place edit
sed -i 's/dev/prod/g' config.txt

Always be careful with -i because it modifies the original file.

4. cut

Extract fields.

cut -d: -f1 /etc/passwd

CSV example:

cut -d, -f2 users.csv

-d specifies the delimiter and -f selects fields.

5. sort
sort names.txt        # Alphabetical
sort -n numbers.txt   # Numerical
sort -r names.txt     # Reverse
sort -u names.txt     # Unique
6. uniq

Remove adjacent duplicates:

sort names.txt | uniq

Count occurrences:

sort names.txt | uniq -c

For counting, sort first because uniq only compares adjacent lines.

7. tr
Translate characters
echo "hello" | tr 'a-z' 'A-Z'

Output:

HELLO
Delete characters
echo "abc123" | tr -d '0-9'

Output:

abc
8. wc
wc -l file.txt   # Lines
wc -w file.txt   # Words
wc -c file.txt   # Bytes

Example:

cat *.log | wc -l
9. head / tail
First 10 lines
head file.txt
First N lines
head -n 20 file.txt
Last N lines
tail -n 20 app.log
Follow a log
tail -f app.log
Task 6: Useful Patterns and One-Liners
1. Find files older than N days
find /var/log -type f -name "*.log" -mtime +7

Delete them:

find /var/log -type f -name "*.log" -mtime +7 -delete

Use -delete carefully, especially in production.

2. Count lines in all .log files
wc -l *.log

Total:

cat *.log | wc -l
3. Replace a string across files
sed -i 's/old-value/new-value/g' *.conf

For a directory:

grep -rl "old-value" ./config | xargs sed -i 's/old-value/new-value/g'
4. Check whether a service is running
systemctl is-active --quiet nginx && echo "Running" || echo "Stopped"
5. Monitor disk usage
df -h

Simple alert:

df -P / | awk 'NR==2 {gsub("%","",$5); if ($5 > 80) print "ALERT: Disk usage is", $5 "%"}'
6. Tail logs and filter errors
tail -f app.log | grep --line-buffered -i "error"

Useful during live troubleshooting.

7. Count HTTP errors in a log
grep -E ' 5[0-9][0-9] ' access.log | wc -l
8. Find the largest files
du -ah /var/log | sort -rh | head -20

Useful when investigating disk-space issues.

Task 7: Error Handling and Debugging
1. Exit Codes

Linux commands normally return:

0 = success
non-zero = failure

Example:

ls /tmp
echo $?

Explicit exits:

exit 0

Success:

exit 1

Failure:

exit 1
2. set -e

Exit the script when a command fails.

#!/bin/bash

set -e

mkdir /some/directory
echo "This runs only if mkdir succeeds"
3. set -u

Treat unset variables as errors.

#!/bin/bash

set -u

echo "$UNDEFINED_VAR"

The script will fail because the variable is unset.

4. set -o pipefail

Normally, a pipeline can hide an earlier command's failure.

set -o pipefail

false | true

echo $?

With pipefail, the pipeline returns a failure when an important command in the pipeline fails.

5. set -x

Print commands as Bash executes them.

#!/bin/bash

set -x

NAME="Govind"
echo "Hello $NAME"

Useful for debugging scripts.

Disable it with:

set +x
6. Trap

Use trap to execute cleanup code when the script exits.

cleanup() {
    echo "Cleaning up..."
    rm -f /tmp/app.lock
}

trap cleanup EXIT

touch /tmp/app.lock

echo "Running application..."

A common production pattern:

#!/bin/bash

set -euo pipefail

cleanup() {
    rm -f /tmp/myapp.lock
}

trap cleanup EXIT

echo "Starting deployment..."
Recommended DevOps Script Template

A useful starting template for your own automation scripts:

#!/bin/bash

set -euo pipefail

SCRIPT_NAME=$(basename "$0")

usage() {
    echo "Usage: $SCRIPT_NAME <environment>"
    exit 1
}

cleanup() {
    echo "Cleaning up..."
}

trap cleanup EXIT

if [ "$#" -ne 1 ]; then
    usage
fi

ENVIRONMENT="$1"

case "$ENVIRONMENT" in
    dev|test|prod)
        echo "Deploying to $ENVIRONMENT"
        ;;
    *)
        echo "Invalid environment: $ENVIRONMENT"
        exit 1
        ;;
esac

echo "Deployment started..."

This combines arguments, variables, functions, case, validation, error handling, and traps into a pattern you can reuse in DevOps automation.
