# 🐚 Introduction to the Bash Shell 

A comprehensive, hands-on guide to navigating the Linux command line interface (CLI) using the Bash shell. Learn fundamental file operations, I/O redirection, pipeline data streaming, and automated shell scripting frameworks tailored for Red Hat System Administration (RHCSA) certification prep.

## 🎯 Objectives
By the end of this lab, you will be able to:
* **Access and navigate** the Linux command line interface securely using Bash.
* **Execute** fundamental Bash commands for robust file and directory operations.
* **Understand and implement** input/output redirection and piping data-stream techniques.
* **Create, modify, and execute** basic automation shell scripts.
* **Apply** command-line skills required for core RHCSA certification tracks.

## 📋 Prerequisites
* Basic conceptual understanding of computer file systems and directory structures.
* Familiarity with baseline text editing concepts.
* *No prior Linux or command-line experience required.*
* Access to a modern web browser for the cloud-based lab console.

---

## 💻 Lab Environment Setup

### ☁️ Ready-to-Use Cloud Machines
Al Nafi provides pre-configured Linux-based cloud machines for this lab. Simply click the **Start Lab** button on your dashboard to instantly spin up your dedicated node. No local virtual machines or installation packages are required.

**Your cloud machine includes:**
* CentOS/RHEL-based Linux enterprise distribution.
* Bash shell profile environment (version 4.2 or higher).
* Standard Linux utilities, system packages, and command line text editors.
* Full administrative `root` or `sudo` access loop rights.

---

## 🚀 Lab Tasks

### 🔍 Task 1: Access the Command Line Interface

#### 📡 Subtask 1.1: Connect to Your Lab Environment
1. Click the **Start Lab** button inside your Al Nafi dashboard workspace.
2. Wait for the cloud engine virtual initialization phase to finish (takes ~30-60 seconds).
3. Once fully ready, interact directly with the embedded terminal console window or desktop.
4. *Note: If a desktop interface appears, locate and open the **Terminal** application.*

#### ✅ Subtask 1.2: Verify Your Shell Environment
Audit your active shell interpreter to ensure compliance:
```bash
echo $SHELL
```
Verify the active Bash compilation version layer:
```bash
bash --version
```
Print your active profile account username string:
```bash
whoami
```
Display the current global host system calendar date and clock time:
```bash
date
```
**Expected Output Example:**
```text
[student@lab-machine ~]\(echo\)SHELL
/bin/bash
[student@lab-machine ~]\$ whoami
student
```

---

### 📂 Task 2: Use Basic Bash Commands

#### 🧭 Subtask 2.1: Navigate the File System
Print your absolute location path index within the global system directory tree:
```bash
pwd
```
Enumerate the visible entries located inside your immediate working path directory:
```bash
ls
```
List comprehensive details (permissions, sizes, ownership logs) for tracking files:
```bash
ls -l
```
Expose all items inside the tracking zone, including hidden configuration system files:
```bash
ls -la
```
Jump completely over to the structural root base folder path of the storage system:
```bash
cd /
ls
```
Return directly to the safety zone of your user's isolated home storage path directory:
```bash
cd ~
```

#### 🏗️ Subtask 2.2: Create and Manage Directories
Generate a clean folder node designated for holding your development experiments:
```bash
mkdir lab1-practice
cd lab1-practice
```
Spawn multiple distinct sub-folders concurrently using a single execution string:
```bash
mkdir scripts documents backups
```
Construct a deeply nested, multi-tier subdirectory pathway layout structure recursively:
```bash
mkdir -p projects/web/html projects/web/css
```
Instruct the filesystem engine to print a recursive structural layout map of your adjustments:
```bash
ls -R
```

#### 📝 Subtask 2.3: Create and Manage Files
Instantiate an empty, unallocated structural document index tag:
```bash
touch readme.txt
```
Stream a custom raw string block path and redirect it to generate a new populated document:
```bash
echo "Welcome to Bash Shell Lab" > welcome.txt
```
Print the contents of your newly written file object straight back onto the workspace console:
```bash
cat welcome.txt
```
Utilize a standard "Here Document" (`<< EOF`) delimiter structure to build a multi-line report:
```bash
cat > system-info.txt << EOF
System Information Lab File
Created on: \$(date)
User: \$(whoami)
Directory: \$(pwd)
EOF
```
Verify the dynamic output results embedded inside your generated report text:
```bash
cat system-info.txt
```

#### 🔄 Subtask 2.4: Copy, Move, and Remove Files
Duplicate an existing documentation target file directly into a sub-folder container path:
```bash
cp welcome.txt documents/
```
Create a replica of the tracking file while assigning it a different name index flag:
```bash
cp welcome.txt welcome-backup.txt
```
Relocate a resource document away from your active path into an organized repository sub-folder:
```bash
mv readme.txt documents/
```
Rename a documentation tracking file to modify its structural indexing label:
```bash
mv welcome-backup.txt welcome-copy.txt
```
Permanently erase a file node from your persistent local storage blocks:
```bash
rm welcome-copy.txt
```
Verify all structural movements via the console tracking metrics:
```bash
ls
ls documents/
```

---

### 🔀 Task 3: Practice Using Redirection and Piping

#### 📤 Subtask 3.1: Output Redirection
Capture standard command display telemetry and redirect the flow to construct a static data file:
```bash
ls -la > file-listing.txt
cat file-listing.txt
```
Append fresh real-time tracking entries onto the absolute bottom boundary of your logging file without overwriting old blocks:
```bash
date >> file-listing.txt
echo "--- End of listing ---" >> file-listing.txt
cat file-listing.txt
```

#### 📥 Subtask 3.2: Input Redirection
Seed an raw data logging list to use for operational ingestion parsing tests:
```bash
cat > numbers.txt << EOF
10
25
5
30
15
EOF
```
Force the native processing system to ingest file lines via standard input redirection and organize the output values:
```bash
sort < numbers.txt
```
Incorporate multiple redirects simultaneously to parse an input source and export results to an organized log:
```bash
sort < numbers.txt > sorted-numbers.txt
```

#### 🔗 Subtask 3.3: Using Pipes
Stream live list output straight into counting utilities to gauge total item capacity matches:
```bash
ls | wc -l
```
Query running host process charts and intercept target keywords via precise streaming filters:
```bash
ps aux | grep bash
```
Analyze physical drive capacity utilization layouts and format the metrics into a descending size list:
```bash
du -h | sort -hr
```
Extract administrative password mapping data strings matching a targeted master account:
```bash
cat /etc/passwd | grep root
```
Chain multiple logical stream interfaces sequentially to isolate directories and print total directory counts:
```bash
ls -la | grep "^d" | wc -l
```

#### 🛡️ Subtask 3.4: Advanced Redirection Examples
Isolate regular command standard outputs (`stdout`) away from error logs (`stderr`) using channel targets `1` and `2`:
```bash
ls /nonexistent-directory > output.txt 2> error.txt
```
Force dual diagnostic traffic streams to merge and print down into an identical unified tracking document:
```bash
ls /nonexistent-directory > combined.txt 2>&1
```
Incorporate the structural `tee` pipeline splitting block tool to output data onto your display while caching a clone to disk:
```bash
ls -la | tee directory-listing.txt
```

---

### 📜 Task 4: Create and Execute Simple Scripts

#### ⚙️ Subtask 4.1: Create Your First Shell Script
Navigate straight over to your isolated lab automation tracking subdirectory:
```bash
cd ~/lab1-practice/scripts
```
Populate a clean script file containing structural system tracking statements:
```bash
cat > hello-world.sh << 'EOF'
#!/bin/bash
# My first shell script
echo "Hello, World!"
echo "Welcome to Bash scripting!"
echo "Today is: \$(date)"
echo "Current user: \$(whoami)"
EOF
```
Inject explicit binary system execute permissions into the newly generated script file module:
```bash
chmod +x hello-world.sh
```
Trigger execution of your completed script module using explicit local path routing flags:
```bash
./hello-world.sh
```

#### 📊 Subtask 4.2: Create a System Information Script
Assemble an advanced administrative logging tool to gather global diagnostic data from the machine:
```bash
cat > system-report.sh << 'EOF'
#!/bin/bash
# System Information Report Script

echo "=================================="
echo "    SYSTEM INFORMATION REPORT"
echo "=================================="
echo
echo "Date and Time: \$(date)"
echo "Hostname: \$(hostname)"
echo "Current User: \$(whoami)"
echo "Current Directory: \$(pwd)"
echo
echo "System Uptime:"
uptime
echo
echo "Disk Usage:"
df -h | head -5
echo
echo "Memory Usage:"
free -h
echo
echo "Number of files in current directory: \$(ls | wc -l)"
echo "=================================="
EOF
```
Authorize and test execution of the operational analytics script:
```bash
chmod +x system-report.sh
./system-report.sh
```

#### 🗣️ Subtask 4.3: Create an Interactive Script
Construct an automation script capable of intercepting live keyboard inputs during runtime loops:
```bash
cat > user-info.sh << 'EOF'
#!/bin/bash
# Interactive User Information Script

echo "Welcome to the User Information Collector!"
echo

# Get user input
read -p "Enter your name: " username
read -p "Enter your favorite color: " color
read -p "Enter your age: " age

echo
echo "=================================="
echo "User Information Summary"
echo "=================================="
echo "Name: \$username"
echo "Favorite Color: \$color"
echo "Age: \$age"
echo "Script run on: \$(date)"
echo "=================================="

# Create a personalized file
echo "Creating a personalized file for \$username..."
cat > "\${username}-info.txt" << EOF
Personal Information File
Name: \$username
Favorite Color: \$color
Age: \$age
File created: \$(date)
EOF

echo "File \${username}-info.txt has been created!"
EOF
```
Authorize and run the interactive input parsing loop tool:
```bash
chmod +x user-info.sh
./user-info.sh
```

#### 🧹 Subtask 4.4: Create a File Management Script
Build a highly practical utility script to handle automated folder creation and target document data backups:
```bash
cat > file-manager.sh << 'EOF'
#!/bin/bash
# Simple File Management Script

echo "File Management Utility"
echo "======================"

# Create backup directory
backup_dir="backup-\$(date +%Y%m%d)"
mkdir -p "\$backup_dir"

# List current files
echo "Current files in directory:"
ls -la

echo
echo "Creating backup of .txt files..."

# Backup all .txt files
for file in *.txt; do
    if [ -f "\$file" ]; then
        cp "\(file" "\)backup_dir/"
        echo "Backed up: \$file"
    fi
done

echo
echo "Backup completed in directory: \$backup_dir"
echo "Contents of backup directory:"
ls -la "\$backup_dir"
EOF
```
Authorize execution bounds and trigger the automated backup routing process:
```bash
chmod +x file-manager.sh
./file-manager.sh
```

---

## 🏁 Conclusion
You have achieved core fluency in the operational fundamentals of the Bash workspace environment:
* Interacted confidently with terminal pathways, directory nests, and file indexing structures.
* Connected complex processing modules using advanced streaming parameters, redirections, and pipelines.
* Authored clean, functional shell scripts to run interactive logic or system tracking operations.

---

## 🩺 Troubleshooting Tips

### 🛑 Permission Denied Error
* **Cause:** The destination file object lacks explicit system execute rights.
* **Fix:** Apply the initialization flag shift via terminal commands: `chmod +x scriptname.sh`.

### 🔍 Command Not Found Error
* **Cause:** The current directory context path isn't bound into your environment `$PATH` search rings.
* **Fix:** Prepend direct local folder traversal indicators directly onto the call statement: `./scriptname.sh`.

### 🪲 Script Parsing Syntax Errors
* **Cause:** Missing encapsulating string quote tokens or poorly spaced evaluation parameter blocks inside execution paths.
* **Fix:** Trace script logical operations or review loop conditions line-by-line using structural script trace calls: `bash -x scriptname.sh`.
