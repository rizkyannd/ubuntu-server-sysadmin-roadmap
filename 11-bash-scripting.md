# Write Simple Bash Script for Automation

## 🧭 Context
In this step, I learned the fundamentals of Bash scripting—covering error handling, input/output, variables, conditionals, loops, functions, command-line arguments, and exit codes.

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Tools:** `bash`, text editor (`nano`)

## 📋 Hands-On Practice

### 1. Shebang & Error Handling
```bash
#!/bin/bash
set -u
set -e
set -o pipefail
```

### 2. Input & Output
```bash
read -p "Enter username: " USERNAME
echo "Hello, sysadmin!"
echo "Hello, $USERNAME"
```

### 3. Variables & Assignment
```bash
TODAY=$(date +%Y-%m-%d)
echo "Today is: $TODAY"
```

### 4. Conditionals (if/elif/else)
```bash
DISK_USAGE=$(df / | awk 'NR==2 {print$5}' | tr -d '%')
if [ "$DISK_USAGE" -gt 80 ]; then
        echo "Disk usage: [WARNING]$DISK_USAGE% !!!"
elif [ "$DISK_USAGE" -gt 60 ]; then
        echo "Disk usage: [CAUTION]$DISK_USAGE%"
else
        echo "Disk usage: [OK]$DISK_USAGE%"
fi

if [ -d "test/backup" ]; then
        echo "Backup directory exists"
else
        mkdir -p test/backup
        echo "Backup directory created"
fi
```
Checks the server's disk usage with multi-tiered thresholds and ensures a backup directory is available before proceeding.

### 5. Loops (for & while)
```bash
for FILE in /home/kaks/*; do
        echo "FILE - {$FILE}"
done

COUNT=1
while [ $COUNT -lt 6 ]; do
        echo "Count: $COUNT"
        COUNT=$((COUNT + 1))
done
```

### 6. Functions
```bash
backup_folder() {
        local SOURCE=$1
        local DEST=$2
        tar -czf "$DEST/backup-$(date +%Y-%m-%d).tar.gz" "$SOURCE"
        echo "Backup completed: $DEST"
}

read -p "Source backup: " sour
read -p "Destination backup: " dest
backup_folder "$sour" "$dest"
```
The `backup_folder` function compresses the source folder into a `.tar.gz` archive in the destination directory, automatically appending the date to the filename.

### 7. Command-Line Arguments
```bash
echo "Filename            : $0"
echo "Argument 1          : $1"
echo "Argument 2          : $2"
echo "Argument 3          : $3"
echo "All arguments       : $@"
echo "Argument count      : $#"
```

### 8. Exit Codes
```bash
read -p "New directory name: " FOLDERNEW
mkdir "$FOLDERNEW"
if [ $? -eq 0 ]; then
        echo "==> DIRECTORY '$FOLDERNEW' CREATED SUCCESSFULLY"
else
        echo "==> FAILED TO CREATE DIRECTORY"
        exit 1
fi
```

## 🧩 Key Takeaways

**Surprised That Bash Doesn't Automatically Stop on Errors**

I originally expected Bash to behave like Python in an IDE—if a line throws an error, the program immediately halts and prints an error message. This assumption came from running manual terminal commands, which always output errors like "No such file or directory" or "command not found." However, interactive terminal sessions are a different context. While individual shell commands show errors immediately, Bash scripts default to executing subsequent lines even if a previous command fails. Realizing this helped me understand why `set -u`, `set -e`, and `set -o pipefail` need to be explicitly declared at the start of a script.

**Understanding Exit Codes (`$?`)**

I hadn't considered that every executed command returns an exit status code that can be checked programmatically. Initially, I was unsure how to programmatically verify whether a command succeeded beyond inspecting screen output. Discovering `$?` (which stores the previous command's exit code) made it clear how to use conditional statements (`if`/`else`) to control script flow—for example, proceeding only if `mkdir` succeeds (`$? -eq 0`), or failing fast with `exit 1` and a clean error message if it fails.

**Indentation is Optional in Bash**

Unlike Python, where indentation defines block structure, whitespace and indentation are optional in Bash. Despite this, maintaining clean indentation keeps scripts readable and easy to follow. Syntactically, Bash feels slightly lower-level than Python, but knowing core concepts (variables, conditionals, loops, functions) from Python and Java made picking up Bash syntax straightforward.

## 📸 Screenshots

**1. Comparison without vs. with `set -u`/`set -e` — scripts without error flags continue executing after errors, while adding `set -u` halts execution immediately at the problematic line (`unbound variable`):**

<img width="739" height="181" alt="image" src="https://github.com/user-attachments/assets/7e16dc45-c68a-4df3-9728-be212d73c508" />

**2. The `backup_folder` function — prompts for source & destination paths, successfully generating a `.tar.gz` archive (verified via `ls -l`):**

<img width="662" height="249" alt="image" src="https://github.com/user-attachments/assets/ac252602-da0a-43e8-b243-47c1cfa86465" />

**3. Exit codes (`$?`) in action — successful `mkdir` for a new directory (`$? -eq 0` → success message) vs. failure when trying to create an existing directory (error message):**

<img width="1099" height="295" alt="image" src="https://github.com/user-attachments/assets/bd3fc558-6cf1-49e2-9462-053ae3de14a6" />
