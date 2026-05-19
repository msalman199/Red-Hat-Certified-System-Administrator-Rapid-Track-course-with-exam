# 📂 File System Navigation

A comprehensive, hands-on guide to mastering Linux filesystem traversal, directory organization, and the security mechanics of file permissions. This lab builds foundational command-line skills required for Red Hat Certified System Administrator (RHCSA) certification tracks.

## 🎯 Objectives
By the end of this lab, you will be able to:
* **Navigate** the Linux file system architecture confidently using command-line tools.
* **Use** `cd`, `ls`, and `find` commands effectively to locate system objects.
* **Create and organize** nested directory trees and file structures.
* **Understand and modify** Linux file/directory permissions using `ls -l` and `chmod`.
* **Apply** fundamental file administration concepts essential for enterprise Red Hat environments.

## 📋 Prerequisites
* Basic conceptual understanding of a command-line interface.
* Familiarity with the concepts of files and folders (directories).
* *No prior Linux experience required*—this lab is built for absolute beginners.
* Access to a modern web browser to connect to the cloud environment.

---

## 💻 Lab Environment Setup

### ☁️ Ready-to-Use Cloud Machines
Al Nafi provides pre-configured Linux-based cloud machines for this lab. Simply click the **Start Lab** button to access your dedicated terminal instance. No local software installation or virtual machine setup is required.

**Your cloud machine includes:**
* CentOS/RHEL-based Linux enterprise distribution.
* Full command-line access via an embedded web terminal.
* All necessary system administration tools and utilities pre-installed.

---

## 🚀 Lab Tasks

### 🔍 Task 1: Master Directory Navigation with cd, ls, and find

#### 🗺️ Subtask 1.1: Understanding Your Current Location
Determine your placement in the file system tree and analyze basic details:
```bash
pwd
```
* **Expected Output:** `/home/student` or `/root`

List the contents of your current working directory:
```bash
ls
```
Display comprehensive long-format metadata parameters (permissions, ownership, size, and modification stamps):
```bash
ls -l
```

#### 🧭 Subtask 1.2: Basic Directory Navigation
Traverse up to the absolute root base path of the operating system:
```bash
cd /
ls
```
Move into the user home base directory structure:
```bash
cd /home
ls -l
```
Instantly return to your current user's isolated home directory space using the tilde shortcut:
```bash
cd ~
pwd
```

#### 📈 Subtask 1.3: Advanced Navigation Techniques
Step backward exactly one level into the parent directory node:
```bash
cd ..
pwd
```
Toggle straight back to your previous active directory path:
```bash
cd -
```
Navigate using relative paths (safely bypassing errors if the `Documents` path does not exist yet):
```bash
ls
cd Documents 2>/dev/null || echo "Documents directory doesn't exist yet"
```

#### 🔍 Subtask 1.4: Using the find Command
Return home and perform precise object lookups across your directories:
```bash
cd ~
```
Recursively locate all regular **files** matching inside your active tree:
```bash
find . -type f
```
Recursively locate all **directories** matching inside your active tree:
```bash
find . -type d
```
Search case-insensitively within system configurations for configuration files, suppressing access errors and printing only the first 10 matches:
```bash
find /etc -name "*.conf" -type f 2>/dev/null | head -10
```

---

### 🏗️ Task 2: Create Directories and Files

#### 📁 Subtask 2.1: Creating Directory Structure
Navigate home and spin up a new primary environment practice folder:
```bash
cd ~
mkdir lab2_practice
ls -l
```
Create multiple unlinked tracking folders simultaneously inside a single step loop:
```bash
mkdir projects documents scripts
```
Deploy a deeply nested, multi-tier subdirectory chain sequentially using the parent generation flag:
```bash
mkdir -p lab2_practice/level1/level2/level3
```
> 💡 **Key Concept:** The `-p` flag instructs the utility to dynamically build any missing parent folder nodes in the path chain without throwing conflict exceptions if the folders exist.

Verify the finished multi-tier nested directory landscape recursively:
```bash
ls -R lab2_practice
```

#### 📄 Subtask 2.2: Creating Files
Move into the active deployment workspace folder:
```bash
cd lab2_practice
```
Initialize blank system file index tracking nodes:
```bash
touch readme.txt
touch file1.txt file2.txt file3.txt
```
Stream a custom string and redirect standard output to generate a populated text document:
```bash
echo "This is my first Linux file" > welcome.txt
```
Author a multi-line parameter report document using a functional "Here Document" (`<< EOF`) data block:
```bash
cat > myinfo.txt << EOF
Name: Student
Course: Red Hat System Administration
Lab: File System Navigation
Date: \$(date)
EOF
```
Verify the storage metadata list for all your newly compiled file blocks:
```bash
ls -l
```

#### 🧹 Subtask 2.3: Organizing Files into Directories
Relocate your tracking file into the first tier of the nested directory layout:
```bash
mv file1.txt level1/
```
Bulk-move multiple files downstream deeper into the second-tier structural directory layout:
```bash
mv file2.txt file3.txt level1/level2/
```
Replicate a documentation file down into the deepest level-3 directory boundary:
```bash
cp welcome.txt level1/level2/level3/
```
Run a structural scan to confirm the new clean file layout organization:
```bash
find . -type f
```

---

### 🔐 Task 3: Explore File Permissions with ls -l and chmod

#### 🧠 Subtask 3.1: Understanding File Permissions
Print out full metadata fields to evaluate active access configurations:
```bash
ls -l
```
##### 🗺️ Permission Field Format Breakdown:
```text
-rw-rw-r--  1 student student   29 Nov 15 10:30 welcome.txt
drwxrwxr-x  3 student student 4096 Nov 15 10:25 level1
^

|-- First Character: Shows file type ('-' = regular file, 'd' = directory)
|-- Next 9 Characters: Split into 3 discrete ownership triads (User, Group, Others)
    r = Read (4)  |  w = Write (2)  |  x = Execute (1)
```

#### 🔢 Subtask 3.2: Understanding Permission Numbers
Generate a test file asset and profile its underlying octal permission format weights:
```bash
echo "Permission test file" > permissions_test.txt
ls -l permissions_test.txt
stat -c "%a %n" permissions_test.txt
```

#### 🛠️ Subtask 3.3: Modifying File Permissions
Strip write access rights completely away from the Group and Others entities using numeric notation:
```bash
chmod 644 permissions_test.txt
ls -l permissions_test.txt
```
Elevate ownership rights to allow the User account to execute the file:
```bash
chmod 744 permissions_test.txt
ls -l permissions_test.txt
```
Apply symbolic syntax modifiers to add execute rights specifically to the Group triad:
```bash
chmod g+x permissions_test.txt
```
Symbolically strip read permissions away from the completely unprivileged Others category:
```bash
chmod o-r permissions_test.txt
```
Audit the finalized file protection matrix state:
```bash
ls -l permissions_test.txt
```

#### 📂 Subtask 3.4: Working with Directory Permissions
Create a scratch practice directory and evaluate its directory properties:
```bash
mkdir permission_test_dir
ls -ld permission_test_dir
chmod 755 permission_test_dir
```
Seed a dummy text tracking asset inside the target directory path:
```bash
echo "Directory test" > permission_test_dir/test_file.txt
```
Strip the execute permission flag away from the root directory to analyze how path mechanics change:
```bash
chmod 644 permission_test_dir
```
Attempt to access or scan the contents of the modified directory boundary:
```bash
ls permission_test_dir
```
* **Expected Outcome:** The system blocks execution with a `Permission denied` error string because directory access requires the active execution (`x`) bit flag.

Restore normal operations by injecting standard execution permissions back into the path:
```bash
chmod 755 permission_test_dir
ls permission_test_dir
```

#### 📜 Subtask 3.5: Practical Permission Scenarios
Compile a localized automation shell script tracking report:
```bash
cat > myscript.sh << 'EOF'
#!/bin/bash
echo "Hello from my script!"
echo "Current directory: \$(pwd)"
echo "Current user: \$(whoami)"
EOF
```
Verify script file states and attempt a localized path execution run:
```bash
ls -l myscript.sh
./myscript.sh
```
* **Expected Outcome:** The system flags an error block because newly touched files lack execute rights by default.

Inject global binary execution rights into the automation script profile and run it:
```bash
chmod +x myscript.sh
./myscript.sh
ls -l myscript.sh
```

---

## 🩺 Troubleshooting Tips

* **Issue: "Permission denied" on directories:** The directory is missing its execution bit flag. Verify using `ls -ld <dir_name>` and apply properties using `chmod +x <dir_name>`.
* **Issue: "No such file or directory":** Your terminal focus is placed incorrectly. Map your true alignment location using `pwd` and poll active paths with `ls`.
* **Issue: Blocked file writes:** Your user profile lacks write bounds inside your working directory. Inspect current location access flags using `ls -ld .`.
* **Issue: `find` floods your screen:** Narrow tracking scopes by appending limits like `head -n 10` or refining search query syntax patterns.

### ✅ Verification Commands
Verify the state of your configurations at any point using these commands:
```bash
pwd                                  # Validates active directory tracking paths
ls -la                               # Displays all hidden and standard system details
tree . 2>/dev/null || find . -type d  # Maps recursive directory structural layouts
stat -c "%a %n" <filename>           # Prints exact active octal protection weights
```

---

## 🏁 Lab Summary and Conclusion
Congratulations! You have successfully completed **Lab 2: File System Navigation**. 

### Key Skills Developed
* **Navigation Mastery:** Managed non-destructive file movements, shortcut paths, and localized lookup scans using `cd`, `ls`, and `find`.
* **Structural Layout Architecture:** Generated clean structural tree maps and safely handled nested directory layouts using `mkdir -p`.
* **Permission Administration:** Evaluated security permissions, modified structural protections dynamically using both octal and symbolic methods (`chmod`), and resolved service execution blockages.

***

If you want to expand this lab guide, let me know if you would like to include an **advanced target find command cheat sheet table** or a **symbolic vs. octal permission weighting comparison matrix**!
