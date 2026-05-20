# Managing Logical Volume Management (LVM)

A comprehensive lab guide designed to master the implementation and lifecycle management of Logical Volume Management (LVM) on enterprise Linux systems [1]. You will learn to aggregate physical disks into abstracted storage pools, provision virtual block structures dynamically, scale storage allocations live, and shrink mounted volumes safely.

---

## 🎯 Objectives
By the end of this lab, you will be able to:
* **Understand** structural LVM layers: Physical Volumes (PV), Volume Groups (VG), and Logical Volumes (LV).
* **Initialize** abstract storage spaces on raw blocks using `pvcreate`.
* **Aggregate** discrete storage layers into massive, unified storage groups using `vgcreate`.
* **Carve out and provision** dynamic virtual disks via `lvcreate` commands.
* **Scale up** logical layers on demand and resize filesystem parameters on-the-fly.
* **Reduce** disk boundaries through systematic filesystem downscaling operations.
* **Monitor** performance ceilings and physical layout maps using administrative tools.

---

## 🛠️ Prerequisites & Setup

### Prerequisites
Before starting, ensure you have:
* Basic familiarity with Linux filesystem tables and hardware partition layouts.
* Comfort executing commands inside the Linux terminal environment.
* Elevated root system access capabilities via administrative `sudo` rules.

### Lab Environment Setup
This lab is optimized for **Al Nafi Cloud Machines** deployed over standard enterprise **CentOS/RHEL distributions**. The virtual layout delivers multiple unallocated physical block target disk items (`/dev/sdb`, `/dev/sdc`, and `/dev/sdd`) designated exclusively for raw LVM execution practices.

### 📐 LVM Architecture Core Concepts
* **Physical Volume (PV):** Raw physical block partitions or raw hard disks initialized by LVM tools.
* **Volume Group (VG):** The global abstracted storage pool created by uniting one or multiple PV arrays together.
* **Logical Volume (LV):** Functional virtual space blocks carved out of a parent VG—acting exactly like a real hardware drive.
* **Physical Extent (PE):** The underlying monolithic sizing block unit allocated inside a VG cluster (Defaults to 4MB steps).

---

## 🚀 Lab Tasks

### Task 1: Creating Physical Volumes

#### 1.1 Identify Available Storage Devices
Audit baseline platform block structures before shifting disk signatures:
```bash
# Map storage blocks currently visible on the machine
lsblk

# Print physical boundaries across attached hardware drives
sudo fdisk -l

# Check usage data across existing live mounts
df -h
```

#### 1.2 Prepare Disks for LVM
Confirm the unallocated sandbox test disks (`/dev/sdb`, `/dev/sdc`, `/dev/sdd`) contain no active system mounts:
```bash
# Isolate candidate disk properties
lsblk | grep -E "sdb|sdc|sdd"

# Ensure target blocks don't hold active historical table schemas
sudo fdisk -l /dev/sdb
sudo fdisk -l /dev/sdc
sudo fdisk -l /dev/sdd
```

#### 1.3 Create Physical Volumes
Convert raw block environments into compatible LVM-ready structures:
```bash
# Initialize physical volume headers across your target drives
sudo pvcreate /dev/sdb /dev/sdc /dev/sdd

# Inspect the detailed structural maps of all system PV structures
sudo pvdisplay

# Print a condensed overview of the active physical layers
sudo pvs
```

#### 1.4 Examine Physical Volume Details
```bash
# Pull deeply verbose hardware descriptor maps
sudo pvdisplay -v

# Isolate properties on a specific targeted target node
sudo pvdisplay /dev/sdb

# Force a quick systemic scan over active physical blocks
sudo pvscan
```

---

### Task 2: Creating Volume Groups

#### 2.1 Create Your First Volume Group
Stitch separate hardware footprints into a single joint pool configuration named `vg_data`:
```bash
# Unite disk B and disk C into a single pool block array
sudo vgcreate vg_data /dev/sdb /dev/sdc

# Review properties across live storage group mappings
sudo vgdisplay
sudo vgs
```

#### 2.2 Create a Second Volume Group
```bash
# Allocate your third disk line into an independent group profile
sudo vgcreate vg_backup /dev/sdd

# Output verbose descriptors across your active pooling environments
sudo vgdisplay -v

# Check status profiles in a detailed tabular view format
sudo vgs -v
```

#### 2.3 Examine Volume Group Properties
```bash
# Isolate structural descriptors matching your data group profile
sudo vgdisplay vg_data

# Track out down-stream physical items bounded inside the pool architecture
sudo vgdisplay -v vg_data

# Query the running kernel state for active volume groups
sudo vgscan
```

---

### Task 3: Creating Logical Volumes

#### 3.1 Create Logical Volumes with Specific Sizes
Carve out usable space chunks directly out of your unified pools:
```bash
# Provision fixed size targets out from the data group repository pool
sudo lvcreate -L 2G -n lv_documents vg_data
sudo lvcreate -L 1G -n lv_projects vg_data

# Provision an independent target block inside your backup pool
sudo lvcreate -L 500M -n lv_backup vg_backup

# Verify creation metadata blocks
sudo lvdisplay
sudo lvs
```

#### 3.2 Create Logical Volumes Using Percentages
```bash
# Consume precisely half of whatever space remains inside the backup pool
sudo lvcreate -l 50%FREE -n lv_archive vg_backup

# Pull verbose structure summaries across all logical elements
sudo lvdisplay -v

# Output precise hardware map access paths across all live blocks
sudo lvs -o +lv_size,lv_path
```

#### 3.3 Format and Mount Logical Volumes
```bash
# Provision explicit terminal path mount targets inside the VFS tree
sudo mkdir -p /mnt/documents /mnt/projects /mnt/backup /mnt/archive

# Inject an extended-4 file architecture layer over your new block nodes
sudo mkfs.ext4 /dev/vg_data/lv_documents
sudo mkfs.ext4 /dev/vg_data/lv_projects
sudo mkfs.ext4 /dev/vg_backup/lv_backup
sudo mkfs.ext4 /dev/vg_backup/lv_archive

# Link block parameters onto the active directory map targets
sudo mount /dev/vg_data/lv_documents /mnt/documents
sudo mount /dev/vg_data/lv_projects /mnt/projects
sudo mount /dev/vg_backup/lv_backup /mnt/backup
sudo mount /dev/vg_backup/lv_archive /mnt/archive

# Audit space metrics to confirm virtual directory mount maps
df -h | grep -E "documents|projects|backup|archive"
```

---

### Task 4: Extending and Shrinking Volumes

#### 4.1 Extend a Logical Volume Live
Scale up virtual dimensions seamlessly without taking storage structures offline:
```bash
# Check current block configurations
sudo lvs vg_data/lv_documents
sudo vgs vg_data

# Append 1GB of fresh space straight onto the logical layout bounds
sudo lvextend -L +1G /dev/vg_data/lv_documents
sudo lvs vg_data/lv_documents

# Force the filesystem to stretch over the new block boundaries live
sudo resize2fs /dev/vg_data/lv_documents

# Confirm structural updates are live inside the virtual path engine
df -h /mnt/documents
```

#### 4.2 Extend a Volume Group and Maximize Space
```bash
# Review physical pooling capacity limits
sudo vgs vg_data

# Note: If an external physical volume block was available, you would extend via:
# sudo vgextend vg_data /dev/sde

# Tell the logical allocation engine to swallow all available free space remaining
sudo lvextend -l +100%FREE /dev/vg_data/lv_projects

# Sync filesystem boundaries to fully absorb updated block layouts
sudo resize2fs /dev/vg_data/lv_projects
df -h /mnt/projects
```

#### 4.3 Shrink a Logical Volume (Destructive Safety Pipeline)
> ⚠️ **CRITICAL WARNING:** Filesystem downscaling operations carry heavy data-corruption risks. Always back up target data and take paths offline first.
```bash
# 1. Sever directory connections completely
sudo umount /mnt/archive

# 2. Execute a mandatory disk health check scan pattern over the blocks
sudo e2fsck -f /dev/vg_backup/lv_archive

# 3. Downscale the underlying filesystem layout first (compressing down to 200MB)
sudo resize2fs /dev/vg_backup/lv_archive 200M

# 4. Clip off trailing block parameters from the underlying logical envelope
sudo lvreduce -L 200M /dev/vg_backup/lv_archive
# Type 'y' when prompted by the interactive terminal to approve modifications

# 5. Confirm size contractions, re-anchor components, and audit execution metrics
sudo lvs vg_backup/lv_archive
sudo mount /dev/vg_backup/lv_archive /mnt/archive
df -h /mnt/archive
```

#### 4.4 Monitor LVM Components
Generate trace elements and audit physical asset maps across the system:
```bash
# Push transaction verify markers deep inside active folders
echo "This is a test document" | sudo tee /mnt/documents/test.txt > /dev/null
echo "This is a project file" | sudo tee /mnt/projects/project.txt > /dev/null
echo "This is backup data" | sudo tee /mnt/backup/backup.txt > /dev/null
echo "This is archived data" | sudo tee /mnt/archive/archive.txt > /dev/null

# Survey spatial metrics and print structural summaries
df -h | grep -E "documents|projects|backup|archive"
sudo pvs && sudo vgs && sudo lvs
```

---

## 🛠️ Troubleshooting

### Issue 1: Target Device or Path is Locked / Busy
* **Problem:** Filesystem operations like unmounting or reduction fail because a program thread or active user shell maintains an open lock descriptor.
* **Fix:** Isolate blocking application streams, terminate the task tracks, or break the linkages cleanly:
  ```bash
  sudo lsof +D /mnt/archive
  sudo fuser -k -m /mnt/archive
  ```

### Issue 2: Extension Fails Due to Insufficient Free Extents
* **Problem:** `lvextend` steps crash out because your parent volume group has run out of physical extents.
* **Fix:** Introduce an additional initialized physical volume into the pool structure first before allocating virtual segments:
  ```bash
  sudo pvcreate /dev/sde
  sudo vgextend vg_data /dev/sde
  ```

### Issue 3: XFS Volumes Fail During Downscale/Shrink Steps
* **Problem:** The XFS file allocation blueprint is structured non-destructively by default; it does not natively support shrinking size boundaries.
* **Fix:** Standard `lvreduce` operations cannot downscale XFS mounts. You must back up storage objects, erase/rebuild filesystems entirely via `mkfs.xfs`, and reload content components manually.

---

## 🛡️ Best Practices
* **Resize Filesystems in One Step via `-r`:** Skip executing separate `resize2fs` routines manually. Pass the `-r` flag natively inside `lvextend` (e.g., `lvextend -r -L +1G <LV_PATH>`) to update block structures and filesystems simultaneously.
* **Always Unmount Before Reducing Space:** Never risk resizing or reducing logical volumes while they remain active. Unmount the target mount path first, then run a full integrity check using `e2fsck` beforehand.
* **Use Descriptive, Meaningful Names:** Avoid confusing default names. Use clear, descriptive labels for volume groups and logical tracks (e.g., `vg_prod` and `lv_mysql`) to prevent costly infrastructure configuration errors.
