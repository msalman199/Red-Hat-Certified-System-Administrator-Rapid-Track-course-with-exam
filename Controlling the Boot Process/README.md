# 🛠️ Controlling the Boot Process

This repository contains the comprehensive guide and automated command references for the **Controlling the Boot Process** lab environment.

---

## 🎯 Objectives
By the end of this lab, you will be able to:
* 🔹 Understand the `systemd` boot process and service management.
* 🔹 View and analyze `systemd` services and targets.
* 🔹 Modify boot parameters using the GRUB2 bootloader.
* 🔹 Access and utilize rescue mode for troubleshooting boot failures.
* 🔹 Implement basic boot process troubleshooting techniques.
* 🔹 Configure system startup behavior through `systemd` targets.

## 📋 Prerequisites
Before starting this lab, you should have:
* 🔹 Basic understanding of the Linux command line interface.
* 🔹 Familiarity with file system navigation and text editing.
* 🔹 Knowledge of basic system administration concepts.
* 🔹 Understanding of user privileges and `sudo` command usage.
* 🔹 Completion of previous labs covering basic Linux operations.

## 💻 Lab Environment
* **Platform**: Pre-configured Linux cloud machines.
* **OS Distribution**: CentOS/RHEL 8 or 9 system with `systemd`.
* **Bootloader**: GRUB2 pre-installed.
* **Privileges**: Full root/sudo access for system-level operations.

---

## 🚀 Lab Tasks

### 📂 Task 1: View systemd Services and Targets

#### 🔍 Subtask 1.1: Understanding systemd Basics
```bash
# Step 1: Check the current systemd version and system status
systemctl --version

# Step 2: View the overall system status (uptime, active units, load, and logs)
systemctl status
```

#### 🔍 Subtask 1.2: Exploring systemd Services
```bash
# Step 3: List all active services
systemctl list-units --type=service --state=active

# Step 4: List all services (both active and inactive)
systemctl list-units --type=service --all

# Step 5: Check the status of a specific service (SSH service)
systemctl status sshd

# Step 6: View detailed structural information about a service
systemctl show sshd
```

#### 🔍 Subtask 1.3: Working with systemd Targets
```bash
# Step 7: List all available targets on the platform
systemctl list-units --type=target

# Step 8: Check the current default target configuration
systemctl get-default

# Step 9: View what services are enabled/spawned for the current target
systemctl list-dependencies

# Step 10: Check what services are explicitly required by the graphical target
systemctl list-dependencies graphical.target
```

#### 🔍 Subtask 1.4: Managing Service States
```bash
# Step 11: Practice starting and stopping a service (using chronyd as example)
systemctl status chronyd
sudo systemctl stop chronyd
systemctl status chronyd
sudo systemctl start chronyd
systemctl status chronyd

# Step 12: Enable and disable services for automatic startup behavior
systemctl is-enabled chronyd
sudo systemctl disable chronyd
sudo systemctl enable chronyd
```

---

### ⚙️ Task 2: Modify Boot Parameters with GRUB2

#### ⚙️ Subtask 2.1: Understanding GRUB2 Configuration
```bash
# Step 13: Examine the main GRUB2 runtime environment defaults file
sudo cat /etc/default/grub

# Step 14: View the generated GRUB2 low-level configuration (top 50 lines)
sudo cat /boot/grub2/grub.cfg | head -50
```

#### ⚙️ Subtask 2.2: Temporarily Modifying Boot Parameters
```bash
# Step 15: Reboot the system to access GRUB2 boot loader layout menu
sudo reboot
```
**🕹️ GRUB2 Manual Interaction Steps:**
1. Interrupt the automatic countdown by pressing any key when you see the GRUB2 splash menu (5-second window).
2. Use your keyboard arrow keys to highlight your default kernel entry.
3. Press **`e`** to open the interactive kernel boot line editor.
4. Search for the line configuration that starts with `linux` or `linux16`.
5. Navigate to the absolute end of that line, add a space, and append: `systemd.unit=multi-user.target`
6. Press **`Ctrl+X`** to instantly boot the machine using these temporary settings.

```bash
# Step 16: After the system boots, verify the active target state
systemctl get-default
systemctl list-units --type=target --state=active
```

#### ⚙️ Subtask 2.3: Permanently Modifying Boot Parameters
```bash
# Step 17: Backup and edit the GRUB2 default system configuration
sudo cp /etc/default/grub /etc/default/grub.backup
sudo nano /etc/default/grub

# Step 18: Modify GRUB_CMDLINE_LINUX variable inside /etc/default/grub
# Ensure the "quiet" string parameter is appended to the variables line:
# GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet"

# Step 19: Regenerate the immutable binary GRUB2 boot file configuration
# Run this for standard legacy BIOS systems:
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
# Run this if deploying on a UEFI modern partition layout system instead:
sudo grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg

# Step 20: Change the default systemd target permanently via tool setup
sudo systemctl set-default multi-user.target
systemctl get-default
```

---

### 🚨 Task 3: Use Rescue Mode to Troubleshoot Boot Failures

#### 🚨 Subtask 3.1: Understanding Rescue Mode
1. Reboot the system using `sudo reboot`.
2. At the GRUB2 menu stage, press **`e`** on the targeted kernel string line.
3. Append `systemd.unit=rescue.target` directly to the end of the `linux` line.
4. Execute runtime boot sequence using **`Ctrl+X`**.

```bash
# Step 22: Once in rescue mode, explore the minimal tool environment
systemctl list-units --type=target --state=active
systemctl list-units --type=service --state=active
df -h
journalctl -b
```

#### 🚨 Subtask 3.2: Simulating and Fixing Boot Problems
```bash
# Step 23: Mount the core root filesystem array as read-write and alter fstab config
mount -o remount,rw /
cp /etc/fstab /etc/fstab.backup
echo "/dev/nonexistent /mnt/fake ext4 defaults 0 2" >> /etc/fstab

# Step 24: Exit rescue mode to test your simulated failure boot run
systemctl default
# Note: If the operating system freezes or errors out, hard reboot and re-enter rescue mode.

# Step 25: Fix the broken fstab file via rescue target tool manipulation
mount -o remount,rw /
sed -i '/nonexistent/d' /etc/fstab
cat /etc/fstab  # Check your data file to make sure it is restored cleanly
# Alternate method: cp /etc/fstab.backup /etc/fstab
```

#### 🚨 Subtask 3.3: Using Emergency Mode
1. Execute a system reboot. At the initial GRUB2 menu shell, press **`e`**.
2. Append `systemd.unit=emergency.target` to the targeted `linux` parameter path line.
3. Boot the system environment up using **`Ctrl+X`**.

```bash
# Step 27: Check storage mappings and structures inside emergency mode
systemctl list-units --type=target --state=active
mount
mount -o remount,rw /  # Upgrade filesystem permissions to read-write for recovery tasks
```

#### 🚨 Subtask 3.4: Password Recovery Scenario
```bash
# Step 28: Simulate account recovery from inside rescue target environment
mount -o remount,rw /
passwd root

# Provision a backup emergency root group admin account
useradd -m testuser
passwd testuser
usermod -aG wheel testuser

# Step 29: Restore default graphical operational target targets
systemctl set-default graphical.target
systemctl reboot
```

---

## 🔍 Troubleshooting Tips

### Common Issues and Solutions

* ❌ **Issue 1: GRUB2 selection splash screen doesn't show during boot up**
  * ✔️ **Solution**: The display timer interval configuration value might be set to zero. Edit `/etc/default/grub`, update or append `GRUB_TIMEOUT=5`, and save changes. Recompile the configuration template by calling your layout target configuration tool (`grub2-mkconfig`).

* ❌ **Issue 2: Attempted modifications to files in Rescue/Emergency Mode are failing to save**
  * ✔️ **Solution**: The diagnostic run environments map the core storage nodes as read-only systems automatically to preserve system health. Elevate file permissions to read-write mode before editing file contents:
    ```bash
    mount -o remount,rw /
    ```
