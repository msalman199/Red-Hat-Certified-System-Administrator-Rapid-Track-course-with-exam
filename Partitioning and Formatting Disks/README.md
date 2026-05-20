# Partitioning and Formatting Disks

A comprehensive lab guide designed to master storage administration on Linux systems. You will learn to manipulate disk partition structures via CLI utilities, initialize high-performance filesystems, handle block mount tables, and automate storage mounts across kernel initialization routines.

---

## 🎯 Objectives
By the end of this lab, you will be able to:
* **Create and manage** disk partitions using `fdisk` and `parted` command-line tools.
* **Format** newly initialized block storage volumes using various `mkfs` filesystem engines.
* **Mount and unmount** local blocks manually to safely interface storage paths with the VFS.
* **Differentiate** between legacy MBR partition schemas and modern GPT layout paradigms.
* **Query and verify** live block maps, filesystem profiles, and metadata footprints via system commands.
* **Enforce production-grade storage persistence** patterns through systemic configuration hooks.

---

## 🛠️ Prerequisites & Lab Environment

### Prerequisites
Before starting, ensure you have:
* Basic familiarity with Linux command-line interaction and root system privileges via `sudo`.
* Knowledge of the Virtual File System (VFS) hierarchy and core entry points.
* Conceptual awareness of block volumes, cylinders, partition tables, and directory mapping tracks.

### Lab Environment Setup
This lab runs on **Al Nafi Cloud Machines** mapped over **CentOS/RHEL-based distributions**. The workspace drops you directly into an environment backed by active administrative privileges, standard packaging utilities, and multiple raw, unallocated sandbox target drives (`/dev/sdb`, `/dev/sdc`).

---

## 🚀 Lab Tasks

### Task 1: Create Partitions with `fdisk` and `parted`

#### 1.1 Identify Available Storage Devices
Audit current platform device nodes and storage allocations before initiating structural modifications:
```bash
# Enumerate physical and virtual block devices
lsblk

# Print a master system log of active partition definitions
sudo fdisk -l

# Check spatial distribution across existing storage nodes
df -h
```
*Expected Output: Your system displays a primary OS partition (e.g., `/dev/sda` or `/dev/vda`) paired alongside secondary unallocated devices like `/dev/sdb` and `/dev/sdc`.*

#### 1.2 Create Partitions Using `fdisk` (MBR Pattern)
The `fdisk` utility acts as a stream-editor for master boot record storage definitions. Initialize a legacy block arrangement over the second workspace device node:
```bash
# Execute the fdisk engine over the target drive
sudo fdisk /dev/sdb
```
Interact with the command menu using the exact step sequences mapped below:
1. **Initialize an empty DOS partition layout**: Type `o` and press `Enter`.
2. **Provision the first data boundary partition**:
   * Type `n` (new) and press `Enter`.
   * Type `p` (primary) and press `Enter`.
   * Type `1` (partition index) and press `Enter`.
   * Sector offset: Press `Enter` to accept the baseline defaults (`2048`).
   * Spatial sizing bound: Type `+2G` and press `Enter`.
3. **Provision the second data boundary partition**:
   * Type `n` and press `Enter`.
   * Type `p` and press `Enter`.
   * Type `2` and press `Enter`.
   * Sector offset: Press `Enter` to skip default initialization bounds.
   * Spatial sizing bound: Type `+1G` and press `Enter`.
4. **Audit allocation calculations**: Type `p` to output the calculated mapping properties.
5. **Commit operations directly to disk table maps**: Type `w` to lock down changes.

Validate kernel visibility over the updated disk profile:
```bash
lsblk /dev/sdb
```

#### 1.3 Create Partitions Using `parted` (GPT Pattern)
`parted` provides robust scripting compatibility and natively drives modern GUID Partition Table configurations. Hard-set your third block drive structure:
```bash
# Trigger parted interactively over disk line C
sudo parted /dev/sdc
```
Inside the interactive prompt shell interface loop, issue the following instructions:
```text
(parted) mklabel gpt
(parted) mkpart primary ext4 1MiB 1GiB
(parted) mkpart primary xfs 1GiB 3GiB
(parted) print
(parted) quit
```
Verify mapping outputs straight out of the active shell interface tracker:
```bash
lsblk /dev/sdc
```

---

### Task 2: Format Partitions with `mkfs`

#### 2.1 Format with ext4 File System
```bash
# Inject an extended-4 layout profile across partition block B1
sudo mkfs.ext4 /dev/sdb1

# Re-format while applying a descriptive storage label flag
sudo mkfs.ext4 -L "DataDisk1" /dev/sdb1

# Interrogate unique persistent attributes and structural tags
sudo blkid /dev/sdb1
```

#### 2.2 Format with xfs File System
XFS delivers highly scalable I/O performance optimized for modern enterprise clusters. Initialize the storage engine:
```bash
# Drive XFS framework patterns directly over block profile B2
sudo mkfs.xfs /dev/sdb2

# Assign an explicit operational partition target label tag
sudo mkfs.xfs -L "DataDisk2" -f /dev/sdb2

# Confirm target properties
sudo blkid /dev/sdb2
```

#### 2.3 Format GPT Partitions
```bash
# Format your GUID partitions using matching filesystem parameters
sudo mkfs.ext4 -L "GPTDisk1" /dev/sdc1
sudo mkfs.xfs -L "GPTDisk2" -f /dev/sdc2

# Extract explicit hardware fingerprint tags for both elements
sudo blkid /dev/sdc1 /dev/sdc2
```

---

### Task 3: Mount and Unmount File Systems

#### 3.1 Create Mount Points
```bash
# Provision systemic mounting anchors deep inside the /mnt tree
sudo mkdir -p /mnt/disk1 /mnt/disk2 /mnt/gpt1 /mnt/gpt2

# Audit operational access controls over your directory entries
ls -la /mnt/
```

#### 3.2 Mount File Systems Manually
```bash
# Bridge the underlying hardware structures onto the directory anchors
sudo mount /dev/sdb1 /mnt/disk1
sudo mount /dev/sdb2 /mnt/disk2
sudo mount /dev/sdc1 /mnt/gpt1
sudo mount /dev/sdc2 /mnt/gpt2

# Inspect active space allocations across live filesystems
df -h

# Isolate target file system properties out from the global system tables
mount | grep -E "(sdb|sdc)"
```

#### 3.3 Test File System Access
```bash
# Generate trace validation tracking text targets across our storage spaces
sudo touch /mnt/disk1/test_ext4.txt /mnt/disk2/test_xfs.txt /mnt/gpt1/test_gpt_ext4.txt /mnt/gpt2/test_gpt_xfs.txt

# Pipe down context verification signatures into the underlying files
echo "This is an ext4 file system" | sudo tee /mnt/disk1/test_ext4.txt
echo "This is an xfs file system" | sudo tee /mnt/disk2/test_xfs.txt
echo "This is a GPT ext4 file system" | sudo tee /mnt/gpt1/test_gpt_ext4.txt
echo "This is a GPT xfs file system" | sudo tee /mnt/gpt2/test_gpt_xfs.txt

# Read descriptions out to test validation lines
cat /mnt/disk1/test_ext4.txt /mnt/disk2/test_xfs.txt /mnt/gpt1/test_gpt_ext4.txt /mnt/gpt2/test_gpt_xfs.txt
```

#### 3.4 Unmount File Systems
```bash
# Sever directory anchors cleanly before shifting partition metrics
sudo umount /mnt/disk1
sudo umount /mnt/disk2
sudo umount /mnt/gpt1
sudo umount /mnt/gpt2

# Confirm blocks detached gracefully away from the local directory map
df -h | grep -E "(sdb|sdc)"
ls -la /mnt/disk1/
```

#### 3.5 Configure Persistent Mounts
Ensure filesystems restore automatically during startup cycles using absolute block fingerprints:
```bash
# Safely snapshot the configuration file before editing
sudo cp /etc/fstab /etc/fstab.backup

# Append comments and device lines using nested command substitutions
echo "# Lab 9 - Disk partitions" | sudo tee -a /etc/fstab
echo "UUID=\$(sudo blkid -s UUID -o value /dev/sdb1) /mnt/disk1 ext4 defaults 0 2" | sudo tee -a /etc/fstab
echo "UUID=\$(sudo blkid -s UUID -o value /dev/sdb2) /mnt/disk2 xfs defaults 0 2" | sudo tee -a /etc/fstab

# Test table modifications cleanly against the virtual file structure
sudo mount -a

# Confirm operational success metrics
df -h | grep -E "(disk1|disk2)"
```

---

## 🔍 Verification and Testing
```bash
# 1. Audit active internal structure types
lsblk -f

# 2. View comprehensive storage type summaries
df -hT

# 3. Interrogating tracking blocks inside system mount structures
cat /etc/fstab | tail -5

# 4. Check block properties and layout metrics
sudo fsck -n /dev/sdb1
sudo xfs_info /dev/sdb2
```

---

## 🛠️ Troubleshooting

### Issue 1: Target Device is Busy
* **Problem**: The system flags `target is busy` because a process background thread or shell instance is currently keeping a directory descriptor lock active.
* **Fix**: Force process isolation checks to identify the locking execution paths:
  ```bash
  sudo fuser -m /mnt/disk1
  sudo lsof +D /mnt/disk1
  ```

### Issue 2: Partition Node Is Not Recognized
* **Problem**: The kernel storage subsystems delay indexing block structure updates following raw execution manipulations.
* **Fix**: Force partition entry maps to resynchronize without triggering a machine restart:
  ```bash
  sudo partprobe /dev/sdb
  sudo partprobe /dev/sdc
  ```

### Issue 3: Mount Point Collision Conflicts
* **Problem**: A duplicate mount initialization step trips up when an existing target device configuration mapping line is already holding down the directory target.
* **Fix**: Flush the directory line first before redeploying the drive mount configuration:
  ```bash
  sudo umount -f /mnt/disk1 2>/dev/null || true
  sudo mount /dev/sdb1 /mnt/disk1
  ```

---

## 🛡️ Best Practices
* **Mount with UUIDs, Not Device Names:** Device names (like `/dev/sdb1`) can dynamically shift change loops across server restarts. Use unique UUID hashes inside `/etc/fstab` to preserve mount configurations safely.
* **Run a Dry-Run Test Over fstab Changes:** Always execute `sudo mount -a` before restarting a system after modifying `/etc/fstab`. A bad entry can hang your server's entire boot cycle.
* **Check Space Allocations with `df -hT`:** Include the `-T` flag when querying storage stats to quickly view active file types alongside your space tracking metrics.
