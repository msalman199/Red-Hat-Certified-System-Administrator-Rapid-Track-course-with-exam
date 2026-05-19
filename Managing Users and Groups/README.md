# 👥 Managing Users and Groups Lab

A comprehensive, hands-on guide to mastering enterprise user administration and structural access control on Linux. Learn to securely provision identity frameworks, organize operational group matrices, audit backend security databases, and implement stringent password aging security standards essential for Red Hat Certified System Administrator (RHCSA) certification goals.

## 🎯 Objectives
By the end of this lab, you will be able to:
* **Create** new user accounts using the `useradd` command with specialized runtime flags.
* **Set and modify** interactive local authentication credentials using the `passwd` utility.
* **Modify** active account profiles, structures, and environment paths using `usermod`.
* **Create and manage** organizational access directories using `groupadd` and `groupmod`.
* **Manage group memberships** by dynamically binding or removing identities across access groups.
* **Delete users and groups safely** via non-destructive and recursive purging paths using `userdel` and `groupdel`.
* **Understand and audit** the fundamental field layouts of the structural system identity vaults (`/etc/passwd`, `/etc/group`, and `/etc/shadow`).
* **Apply** rigorous real-world credential protection policies and hardening practices.

## 📋 Prerequisites
* Basic conceptual fluency with the Linux command line interface.
* Baseline familiarity with standard file permissions and security ownership frameworks.
* Conceptual knowledge of native directory mappings and the global system layout.
* Administrative terminal access backed by full root or `sudo` elevated operational loops.

---

## 💻 Lab Environment Setup

### ☁️ Ready-to-Use Cloud Machines
Al Nafi provides pre-configured Linux-based cloud environments tailored natively for this assignment. Simply select the **Start Lab** command node on your navigation panel to mount a pre-staged server machine. No local execution steps, hypervisor architectures, or custom software packaging phases are needed.

**Your cloud machine includes:**
* CentOS/RHEL-based enterprise Linux distribution structure.
* Immediate administrative root execution loops available via `sudo` scaling.
* Full integration of core enterprise identity management packages.
* Interactive text display and web terminal access pipes.

---

## 🚀 Lab Tasks

### 🔍 Task 1: Create User Accounts with useradd

#### 🧠 Subtask 1.1: Understanding User Account Basics
Before injecting new records into your infrastructure, perform a diagnostic query across the current system identity catalog:
```bash
# View the tail end of the system's local user database
cat /etc/passwd | tail -5

# Audit your current shell identity scope
whoami

# Forcefully switch execution bounds directly into a persistent root shell loop
sudo su -
```

#### 👤 Subtask 1.2: Create Basic User Accounts
Provision an unprivileged identity record using standard operating system baseline defaults:
```bash
# Provision a standard identity profile
useradd john

# Verify the database successfully captured the record mapping
grep john /etc/passwd

# Confirm the automatic generation of an isolated home storage tree node
ls -la /home/
```

#### 🎛️ Subtask 1.3: Create Users with Custom Options
Deploy accounts with explicit execution variables, unique resource restrictions, and discrete system descriptors:
```bash
# Provision an account mapped to a non-standard home storage pathway
useradd -d /home/custom_jane jane

# Provision an account restricted to a explicit default terminal environment shell
useradd -s /bin/bash mike

# Provision an account with metadata descriptions embedded into the profile notes
useradd -c "Sarah Johnson" sarah

# Provision an account bound to an explicit, manually assigned numeric User ID
useradd -u 1500 tom

# Deploy an identity incorporating a full combination of advanced custom requirements
useradd -c "Alice Smith" -d /home/alice_home -s /bin/bash -u 1501 alice
```

#### ✅ Subtask 1.4: Verify User Creation
Execute structural validation loops to check that your provisioned entries align with required attributes:
```bash
# Validate database record properties across all new entries simultaneously
grep -E "john|jane|mike|sarah|tom|alice" /etc/passwd

# Confirm storage nodes generated accurately across custom and standard targets
ls -la /home/

# Validate numeric tracking identifiers and primary group generation metrics
id john
id alice
```

---

### 🔒 Task 2: Set Passwords and Modify User Details

#### 🔑 Subtask 2.1: Set User Passwords
Initialize protection layers for your newly spawned profiles to allow secure interface access loops:
```bash
# Establish active security keys sequentially (enter keys when prompted by the utility)
passwd john
passwd jane
passwd mike
passwd sarah
```

#### 🛠️ Subtask 2.2: Modify User Account Details
Leverage the versatile `usermod` administration platform tool to adjust active parameters inside running profiles:
```bash
# Modify profile tracking notes to append specialized department descriptors
usermod -c "John Doe - Developer" john

# Shift user storage pathway definitions over to an alternate target directory node
usermod -d /home/john_new john

# Upgrade an account's baseline interactive login interface environment profile
usermod -s /bin/zsh sarah

# Restructure an account's unique internal numerical tracking system fingerprint
usermod -u 1502 mike

# Enforce an administrative lockout on an account to block remote login pipelines
usermod -L tom

# Clear an existing operational lock to restore baseline connectivity privileges
usermod -U tom
```

#### 🔍 Subtask 2.3: Verify User Modifications
Confirm that updates have successfully synced across your active directory nodes:
```bash
# Verify modified records display matching parameter strings inside database logs
grep -E "john|sarah|mike|tom" /etc/passwd

# Audit password security lock states to verify system enforcement behaviors
passwd -S john
passwd -S tom

# Validate execution loops by spawning an interactive shell session as the user
su - john
# (Provide the secure pass strings to complete authentication, then exit back to root)
exit
```

---

### 🏢 Task 3: Create and Manage Groups

#### 🏗️ Subtask 3.1: Create New Groups
Spawn logical collaboration repositories to map out directory structures and handle mass profile organization tasks:
```bash
# Initialize core organizational access rings
groupadd developers
groupadd testers
groupadd managers

# Deploy a restricted security tracking ring containing a hardcoded Group ID number
groupadd -g 2000 admins

# Generate a minimal overhead background system tracking directory container profile
groupadd -r sysops
```

#### 📊 Subtask 3.2: Verify Group Creation
Audit system group targets to confirm proper hardware and indexing registration values:
```bash
# Scan the system data log lines to reveal new active groups
grep -E "developers|testers|managers|admins|sysops" /etc/group

# Fetch runtime registry definitions for specific target entities
getent group developers
getent group admins
```

#### 🔗 Subtask 3.3: Add Users to Groups
Manage access scopes by dropping accounts into their appropriate primary or supplemental privilege lists:
```bash
# Rewrite an account's baseline Primary access assignment profile field
usermod -g developers john

# Map an account into multiple independent operational access groups simultaneously
usermod -G testers,managers jane

# Securely append an account into additional groups without wiping out older rings
usermod -a -G developers,admins mike

# Inject user memberships using specialized directory administration tool statements
gpasswd -a sarah developers

# Bulk-populate group allocation matrices using absolute, comma-separated profile lists
gpasswd -M alice,tom testers
```

#### ✅ Subtask 3.4: Verify Group Memberships
Examine account association metrics to prove identity privilege mapping states:
```bash
# Query active tracking boundaries linked directly to a specified identity profile
groups john
groups jane
groups mike

# Pull active member name registration strings straight out of individual directory groups
getent group developers
getent group testers
getent group managers

# Review combined numeric validation tokens to evaluate active mapping sets
id john
id jane
```

#### 🔄 Subtask 3.5: Modify Group Properties
Adjust identification labels or structural markers belonging to your access group blocks:
```bash
# Alter a group's naming index flag without disturbing any active members
groupmod -n development developers

# Shift internal tracking IDs over to modern architecture requirements
groupmod -g 2001 admins

# Query the database to confirm that properties have transitioned safely
getent group development
getent group admins
```

---

### 🗑️ Task 4: Delete Users and Groups Securely

#### ✂️ Subtask 4.1: Remove Users from Groups
Isolate group parameters by severing specific member linkages before beginning account removals:
```bash
# Explicitly strip a defined identity profile out of a selected group list
gpasswd -d tom testers

# Purge an account out of every attached secondary access ring simultaneously
usermod -G "" alice

# Verify structural group boundaries list clean metrics for the targets
groups tom
groups alice
```

#### 🚯 Subtask 4.2: Delete User Accounts Safely
De-provision identities using varying levels of cleanup enforcement based on business policies:
```bash
# Wipe out account records while leaving the underlying home folder structure safe
userdel tom

# Execute a total recursive purge, deleting the identity along with its data drives
userdel -r alice

# Forcefully close active workflows to lock and erase an identity instantly
userdel -f mike

# Verify user database pathways contain no residual references to the profiles
grep -E "tom|alice|mike" /etc/passwd
ls -la /home/
```

#### 🛑 Subtask 4.3: Delete Groups Safely
Remove legacy directory groups out of active kernel tracking pools without breaking profile structures:
```bash
# Check group tracking tables to discover any hidden linked profiles
getent group testers

# Dissolve an active collection ring that contains no user identities
groupdel testers

# Strip remaining members out of operational groupings to allow clean deletion loops
gpasswd -d john development
gpasswd -d sarah development
groupdel development

# Query tracking interfaces to verify total structural termination of targets
getent group testers
getent group development
```

#### 🧹 Subtask 4.4: Clean Up Remaining Test Accounts
Purge remaining lab records to return system state tables back to default clean values:
```bash
# Clear secondary lab identities along with all attached filesystem folders
userdel -r john
userdel -r jane
userdel -r sarah

# Dissolve legacy workspace directory groups completely away from host tables
groupdel managers
groupdel admins
groupdel sysops

# Run final baseline structural validation queries to confirm a successful wipe
grep -E "john|jane|sarah|tom|alice|mike" /etc/passwd
grep -E "developers|testers|managers|admins|sysops" /etc/group
```

---

## 🔬 Advanced User Management Concepts

### 📂 Understanding Key Files
Enterprise administrative actions modify raw configuration lines inside three foundational system security text vaults:

#### 1. The Global Identity Vault: `/etc/passwd`
Stores primary profile details visible across every running application block on the node:
```bash
head -5 /etc/passwd
```
* **Structural Line Fields Format:** `username:password_placeholder:UID:GID:comment_gecos:home_directory:login_shell`

#### 2. The Shared Organizational Mapping: `/etc/group`
Maintains structural directory identifiers and catalogs supplemental member groups:
```bash
head -5 /etc/group
```
* **Structural Line Fields Format:** `groupname:group_password_placeholder:GID:member_username_list`

#### 3. The Isolated Cryptographic Vault: `/etc/shadow`
Maintains secure encrypted authorization signatures and password policy properties. Accessible *only* via root access loops:
```bash
sudo head -5 /etc/shadow
```
* **Structural Line Fields Format:** `username:password_hash:last_modification:min_days:max_days:warning_days:inactivity_grace:expiration_date:reserved`

---

## 🛡️ User Account Security Best Practices

To safeguard production endpoints against intrusion patterns, enforce strict credential lifespan constraints via `chage`:

```bash
# Mandate regular password renewal policies spanning a 90-day validity scope
chage -M 90 username

# Enforce a 7-day stability rest period before users can modify credentials again
chage -m 7 username

# Trigger automated warning prompts to fire starting 7 days prior to key expiration
chage -W 7 username

# Output all chronological aging data parameters for the target identity
chage -l username

# Establish a hard timestamp baseline date to forcefully decommission an identity
chage -E 2026-12-31 username

# Instant Account Lockout: Freeze authentication privileges and terminate access loops
usermod -L -e 1 username
```

---

## 🩺 Troubleshooting Common Issues

### 🔂 Issue 1: User Already Exists Collision Error
* **Symptom:** Terminal returns `useradd: user 'username' already exists` loops.
* **Fix:** Query system data lines to track the old record down. Use `usermod` to update instead of attempting a duplicate creation step:
  ```bash
  getent passwd username
  ```

### 🔐 Issue 2: Permission Denied Exceptions
* **Symptom:** Low-level directory write faults block execution loops during profile creation attempts.
* **Fix:** You are operating from an unprivileged context workspace path. Upgrade privileges to root before modifying identity databases:
  ```bash
  sudo su -
  # Or prepend configuration updates individually: sudo useradd username
  ```

### 🛑 Issue 3: Group Deletion Failure Loops
* **Symptom:** Console alerts throw `groupdel: cannot remove the primary group of user 'username'` exceptions.
* **Fix:** Linux prevents the deletion of a group if it acts as any profile's absolute Primary boundary link. Change the user's primary identity association flag before deleting:
  ```bash
  usermod -g alternate_group_name username
  groupdel target_old_group
  ```

### 🏠 Issue 4: Missing or Broken Home Folders
* **Symptom:** Automated initialization components skip creation or user home traversal throws validation exceptions.
* **Fix:** Manually generate the target workspace directory node and inject required system tracking permissions and security ownership traits explicitly:
  ```bash
  mkdir -p /home/username
  chown username:username /home/username
  chmod 755 /home/username
  ```
