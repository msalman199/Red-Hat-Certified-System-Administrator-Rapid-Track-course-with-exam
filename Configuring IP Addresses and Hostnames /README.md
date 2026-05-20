# 🛠️ Configuring IP Addresses and Hostnames

This repository contains the complete documentation, step-by-step guides, and file directory layout for the **Configuring IP Addresses and Hostnames** lab on Red Hat Enterprise Linux (RHEL).

---

## 🎯 Objectives
By the end of this lab, you will be able to:
* 🔹 Configure static IP addresses using the `nmcli` command-line tool.
* 🔹 Set up and manage network interfaces on Red Hat Enterprise Linux systems.
* 🔹 Modify system hostnames using the `hostnamectl` command.
* 🔹 Understand the relationship between IP addresses, network interfaces, and hostnames.
* 🔹 Troubleshoot basic network connectivity issues.
* 🔹 Verify network configuration changes and ensure they persist across reboots.

## 📋 Prerequisites
Before starting this lab, ensure you have:
* 🔹 Basic understanding of the Linux command-line interface.
* 🔹 Familiarity with text editors like `vi` or `nano`.
* 🔹 Knowledge of basic networking concepts (IP addresses, subnets, gateways).
* 🔹 Understanding of the Linux file system structure.
* 🔹 Access to a terminal with root or `sudo` privileges.

## 💻 Lab Environment
* **Platform**: Pre-configured Linux cloud machines.
* **OS Distribution**: Red Hat Enterprise Linux (RHEL) 8 or 9.
* **Network Stack**: NetworkManager service pre-installed and active.
* **Privileges**: Full root/sudo privileges available for networking tools.

---

## 🚀 Lab Tasks

### 📂 Task 1: Configure Static IP Addresses with nmcli

#### 🔍 Subtask 1.1: Understanding Current Network Configuration
```bash
# Step 1: Check current network connections profiles
nmcli connection show

# Step 2: Display detailed configuration details about active connections
nmcli connection show --active

# Step 3: View current kernel IP address configuration
ip addr show

# Step 4: Check the status of the NetworkManager daemon service
systemctl status NetworkManager
```

#### 🔍 Subtask 1.2: Creating a New Static IP Connection
```bash
# Step 1: Identify your target network hardware interface name
nmcli device status
# Note: Common names include eth0, ens33, or enp0s3. Replace "eth0" in commands below if needed.

# Step 2: Create a brand new connection profile with static network routing settings
sudo nmcli connection add \
    type ethernet \
    con-name "static-connection" \
    ifname eth0 \
    ip4 192.168.1.100/24 \
    gw4 192.168.1.1

# Step 3: Append public DNS name servers to the target connection profile
sudo nmcli connection modify "static-connection" \
    ipv4.dns "8.8.8.8,8.8.4.4"

# Step 4: Force the target connection to use manual (static) addressing parameters
sudo nmcli connection modify "static-connection" \
    ipv4.method manual
```

#### 🔍 Subtask 1.3: Activating and Testing the Static Connection
```bash
# Step 1: Bind and activate the new static connection profile to your device
sudo nmcli connection up "static-connection"

# Step 2: Verify that the static connection profile is active
nmcli connection show --active

# Step 3: Check that the new static IP address matches your interface target layout
ip addr show eth0

# Step 4: Test standard network layer gateway connectivity out to the internet
ping -c 4 8.8.8.8
```

---

### 📂 Task 2: Set Up Network Interfaces

#### 🔍 Subtask 2.1: Managing Multiple Network Connections
```bash
# Step 1: List comprehensive status maps for all available network interface devices
nmcli device show

# Step 2: Provision an alternative secondary backup connection profile on your card
sudo nmcli connection add \
    type ethernet \
    con-name "backup-connection" \
    ifname eth0 \
    ip4 192.168.1.101/24 \
    gw4 192.168.1.1

# Step 3: Map DNS targets and configure manual parameters on your alternative plan
sudo nmcli connection modify "backup-connection" \
    ipv4.dns "1.1.1.1,1.0.0.1" \
    ipv4.method manual
```

#### 🔍 Subtask 2.2: Switching Between Network Connections
```bash
# Step 1: View all configured network connection profiles
nmcli connection show

# Step 2: Switch execution priority profiles on the target device
sudo nmcli connection down "static-connection"
sudo nmcli connection up "backup-connection"

# Step 3: Verify the network card target configuration changed successfully
ip addr show eth0

# Step 4: Test standard network routing functionality via name tags
ping -c 4 google.com
```

#### 🔍 Subtask 2.3: Configuring Connection Auto-Connect
```bash
# Step 1: Configure your core primary connection to launch automatically at startup
sudo nmcli connection modify "static-connection" \
    connection.autoconnect yes

# Step 2: Elevate the auto-activation priority score of the primary asset 
sudo nmcli connection modify "static-connection" \
    connection.autoconnect-priority 10

# Step 3: Block your fallback backup array asset from activating automatically
sudo nmcli connection modify "backup-connection" \
    connection.autoconnect no
```

---

### 📂 Task 3: Modify Hostnames Using hostnamectl

#### 🔍 Subtask 3.1: Understanding Current Hostname Configuration
```bash
# Step 1: Display complete system architecture metadata properties and hostname layout
hostnamectl status

# Step 2: Extract only the current system node hostname
hostname

# Step 3: Direct view inside the file system static hostname allocation point
cat /etc/hostname
```

#### 🔍 Subtask 3.2: Setting Different Types of Hostnames
```bash
# Step 1: Adjust the standard permanent system static hostname configuration line
sudo hostnamectl set-hostname "lab-server-01"

# Step 2: Set up an optional descriptive/pretty hostname tag mapping
sudo hostnamectl set-hostname "Lab Server 01" --pretty

# Step 3: Provision a dynamic kernel-level transient name tracking record
sudo hostnamectl set-hostname "temp-lab-server" --transient

# Step 4: Confirm that all variations updated cleanly on your host setup
hostnamectl status
```

#### 🔍 Subtask 3.3: Configuring Hostname Resolution
```bash
# Step 1: Open your network local address dictionary configuration map file
sudo nano /etc/hosts

# Step 2: Append your custom server resolution targets mapping to the layout:
# 192.168.1.100    lab-server-01.localdomain    lab-server-01

# Step 4: Execute a local loopback ping mapping verification target test run
ping -c 2 lab-server-01

# Step 5: Test translation tracking properties matching your updated map file
nslookup lab-server-01
```

---

## 🔍 Verification and Testing

Execute these system sweeps to guarantee structural integrity across system reboots:

```bash
# Step 1: Power cycle the network mapping controller daemon
sudo systemctl restart NetworkManager

# Step 2: Check profile persistence statuses 
nmcli connection show --active
ip addr show

# Step 3: Check target hostname mapping persistence data matrices
hostnamectl status
hostname -f

# Step 4: Run a full diagnostic check targeting structural data nodes
ping -c 4 8.8.8.8
ping -c 4 google.com
ping -c 2 lab-server-01

# Step 5: Evaluate the performance of local DNS resolution pathways
nslookup google.com
dig google.com
```

---

## 🛠️ Troubleshooting Common Issues

### Network Connection Issues
* ❌ **Problem**: Connection profile fails to switch or activate.
  * ✔️ **Solution**: Check for competing rules running on the device, or misnamed device identifiers. Clear down any blocked tasks manually:
    ```bash
    nmcli connection show
    nmcli device status
    sudo nmcli connection down "conflicting-connection"
    ```
* ❌ **Problem**: Local routing fails out to external internet nodes.
  * ✔️ **Solution**: Inspect the default gateway paths or check for broken file names inside configuration storage spaces:
    ```bash
    ip route show
    cat /etc/resolv.conf
    ```

### Hostname Issues
* ❌ **Problem**: The system hostname reverts back to default values when system reboots.
  * ✔️ **Solution**: The permission bits on the file could be corrupt. Reset configuration security parameters and reload the server manager tool:
    ```bash
    sudo chmod 644 /etc/hostname
    sudo systemctl restart systemd-hostnamed
    ```
* ❌ **Problem**: Local applications cannot reach properties via internal server hostnames.
  * ✔️ **Solution**: Inspect translation records inside system indexing paths to correct typos:
    ```bash
    cat /etc/hosts
    cat /etc/resolv.conf
    ```

---

## 🧹 Lab Cleanup (Optional)

Run these cleanup lines to reset your cloud terminal to standard deployment defaults:

```bash
# Step 1: Wipe clean the custom lab network infrastructure connection layers
sudo nmcli connection delete "static-connection"
sudo nmcli connection delete "backup-connection"

# Step 2: Reset server tracking strings to local target definitions
sudo hostnamectl set-hostname "localhost.localdomain"

# Step 3: Revert modifications applied to the host routing directory files
sudo cp /etc/hosts /etc/hosts.backup
sudo nano /etc/hosts
```

---

## 📁 Repository & File Structure

This directory tree map illustrates where configuration parameters reside within the RHEL subsystem when executing these tasks:

```text
.
├── README.md                             # Comprehensive lab instruction documentation
└── etc/                                  # System configuration files mapping path
    ├── hostname                          # Static machine identifier configuration target
    ├── hosts                             # Local network namespace name translation map
    └── NetworkManager/                   # Core network connection storage pathways
        └── system-connections/
            ├── static-connection.nmconnection  # Primary manual profile specification data
            └── backup-connection.nmconnection  # Secondary redundant fallback profile file
```
