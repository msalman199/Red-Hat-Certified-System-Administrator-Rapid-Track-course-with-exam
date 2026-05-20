# Linux File and Directory Permissions Lab

A comprehensive lab guide designed to teach core Linux permission concepts, permission modification, ownership management, and advanced Access Control Lists (ACLs).

---

## 🎯 Objectives
By the end of this lab, you will be able to:
* **Understand** Linux file permission concepts (Read `r`, Write `w`, Execute `x`).
* **Modify** permissions using `chmod` via numeric (octal) and symbolic notation.
* **Change** file and directory ownership using `chown` and `chgrp`.
* **Implement** Access Control Lists (ACLs) using `setfacl` for granular management.
* **Verify** permission changes using `ls -l` and `getfacl`.
* **Troubleshoot** common permission-related issues in Linux systems.

---

## 🛠️ Prerequisites & Setup

### Prerequisites
Before starting, ensure you have:
* Basic knowledge of the Linux Command Line Interface (CLI).
* Familiarity with navigation (`cd`, `ls`, `pwd`) and file operations (`mkdir`, `echo`).
* Basic understanding of Linux users and groups.
* Access to a terminal with root/sudo privileges.

## 🚀 Lab Tasks

### Task 1: Change File Permissions with `chmod`

#### 1.1 Create Test Files and Examine Default Permissions
```bash
# Create and navigate to working directory
mkdir ~/permissions_lab
cd ~/permissions_lab

# Create test items
echo "This is a regular text file" > textfile.txt
echo "#!/bin/bash" > script.sh
echo "echo 'Hello World'" >> script.sh
mkdir testdir

# View default permissions
ls -l
```

#### 1.2 Using Numeric (Octal) Notation
* **r** = 4 | **w** = 2 | **x** = 1
```bash
# Make script executable (rwxr-xr-x)
chmod 755 script.sh
ls -l script.sh

# Set restrictive permissions on text file (rw-------)
chmod 600 textfile.txt
ls -l textfile.txt

# Test execution
./script.sh
```

#### 1.3 Using Symbolic Notation
* **u** = user | **g** = group | **o** = others | **a** = all
```bash
# Add execute permission for group and others
chmod g+x,o+x script.sh

# Remove write permission for group
chmod g-w textfile.txt

# Set multiple permissions simultaneously
chmod u+rw,g+r,o-rwx textfile.txt
```

#### 1.4 Directory Permissions
```bash
# Populate directory
echo "Directory content" > testdir/file1.txt
echo "More content" > testdir/file2.txt

# Remove execution permission (blocks entering/listing content)
chmod -x testdir
ls testdir
cd testdir

# Restore execution permission
chmod +x testdir
```

---

### Task 2: Modify File Ownership with `chown`

#### 2.1 Create Additional User Account & Group
```bash
# Switch to root
sudo su -

# Create user and group
useradd testuser
passwd testuser # Set password (e.g., password123)
groupadd testgroup

# Assign user to group
usermod -a -G testgroup testuser
exit
```

#### 2.2 Change File Ownership
```bash
# Change owner to testuser
sudo chown testuser ~/permissions_lab/textfile.txt

# Change both owner and group
sudo chown testuser:testgroup ~/permissions_lab/script.sh
```

#### 2.3 Recursive Ownership Changes
```bash
# Change ownership of directory and all its contents recursively
sudo chown -R testuser:testgroup ~/permissions_lab/testdir

# Change group ownership only
sudo chgrp student ~/permissions_lab/textfile.txt
```

---

### Task 3: Set Access Control Lists (ACLs) with `setfacl`

#### 3.1 Install and Verify ACL Support
```bash
# Verify installation
which setfacl getfacl

# Install if missing (RHEL/CentOS)
sudo yum install acl -y

# Install if missing (Ubuntu/Debian)
sudo apt-get install acl -y

# Verify file system support
mount | grep acl
```

#### 3.2 Set Basic ACLs
```bash
cd ~/permissions_lab
echo "ACL test content" > acltest.txt

# View baseline ACL
getfacl acltest.txt

# Grant read/write access to testuser explicitly
setfacl -m u:testuser:rw acltest.txt

# Notice the '+' sign in the permissions string indicating an active ACL
ls -l acltest.txt
```

#### 3.3 Advanced ACL Operations
```bash
# Set ACL for a specific group
setfacl -m g:testgroup:r acltest.txt

# Set default ACLs on a directory (inherited by future files)
setfacl -m d:u:testuser:rwx testdir
setfacl -m d:g:testgroup:rx testdir
getfacl testdir

# Test inheritance
echo "Testing default ACL" > testdir/newfile.txt
getfacl testdir/newfile.txt
```

#### 3.4 Remove and Modify ACLs
```bash
# Remove specific user entry
setfacl -x u:testuser acltest.txt

# Clear all extended ACLs completely
setfacl -b acltest.txt

# Modify multiple entities at once
setfacl -m u:testuser:rw,g:testgroup:r,o::--- acltest.txt
```

---

## 🔍 Verification and Testing

Create automated runtime tests by making a test script:
```bash
cat > permission_test.sh << 'EOF'
#!/bin/bash
echo "=== Permission Testing Script ==="
echo "Current user: \$(whoami)"
echo "Current directory: \$(pwd)"
echo ""
echo "=== File Permissions ==="
ls -l *.txt *.sh 2>/dev/null
echo ""
echo "=== Directory Permissions ==="
ls -ld testdir
echo ""
echo "=== ACL Information ==="
ls -l | grep "+"
echo ""
echo "=== Detailed ACL for acltest.txt ==="
getfacl acltest.txt 2>/dev/null
echo ""
echo "=== Testing file access ==="
[ -r textfile.txt ] && echo "✓ Can read textfile.txt" || echo "✗ Cannot read textfile.txt"
[ -x script.sh ] && echo "✓ Can execute script.sh" || echo "✗ Cannot execute script.sh"
EOF

chmod +x permission_test.sh
./permission_test.sh
```

---

## 🛠️ Troubleshooting

* **Issue 1: Permission Denied Errors**
  * Check current string using `ls -l <filename>`.
  * Verify your group identity mapping with `groups`.
  * Inspect hidden rules using `getfacl <filename>`.
* **Issue 2: ACL Commands Not Found**
  * Execute `sudo yum install acl -y` or `sudo apt-get install acl -y`.
* **Issue 3: ACLs Not Working / Inactive**
  * Ensure the host storage path mounts safely with ACL parameters using `mount | grep acl`.
  * Remount manually if required: `sudo mount -o remount,acl /`.

---

## 🛡️ Best Practices
* **Principle of Least Privilege:** Always grant the absolute minimum permissions required for a user or group to perform their tasks.
* **Directory Traversal:** Remember that users need execution permissions (`+x`) on a directory to navigate into it or access files inside it.
* **Default ACLs:** Use default directory ACLs for shared team folders to automate inheritance rules flawlessly without manual scripts.
