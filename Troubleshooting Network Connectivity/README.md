# 🌐 Troubleshooting Network Connectivity 

This repository contains the complete documentation, guidelines, and commands for the Network Connectivity Troubleshooting Lab. Students will gain hands-on experience using diagnostic utilities, managing configurations with NetworkManager, and implementing security rules via firewalld.

---

## 🎯 Objectives

By the end of this lab, you will be able to:
* **Test connectivity** using `ping`, `traceroute`, and `nslookup`.
* **Interpret** complex network diagnostic output.
* **Manage configurations** using the `nmcli` command line utility.
* **Configure firewalls** by creating rules using `firewalld`.
* **Troubleshoot** common network faults in Linux environments.
* **Apply systematic approaches** to resolve live network issues.

---

## 📋 Prerequisites

Before starting this lab, you should have:
* Basic understanding of the Linux command line interface (CLI).
* Familiarity with terminal navigation and file editing.
* Basic knowledge of networking concepts (IP addresses, DNS, ports).
* Understanding of Linux file permissions and system administration basics.

---

## 💻 Lab Environment Setup

### Ready-to-Use Cloud Machines
Al Nafi provides pre-configured Linux-based cloud machines for this lab. Simply click **Start Lab** to access your environment. No local virtual machines or extra software installations are required.

### Environment Details:
* **OS:** CentOS/RHEL-based Linux system with all network tools pre-installed.
* **Privileges:** Root access for system configuration.
* **Connectivity:** Full internet connectivity for testing external connections.
* **Hardware:** Pre-configured active network interfaces.

---

## 🛠️ Lab Tasks & Instructions

### Task 1: Testing Network Connectivity with Basic Tools

#### Subtask 1.1: Using the ping Command
The `ping` command sends ICMP echo requests to verify host reachability.

* **Step 1: Test Local Connectivity**
  ```bash
  ping -c 4 127.0.0.1
  ```
  *Expected Output:*
  ```text
  PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
  64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.045 ms
  64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.037 ms
  64 bytes from 127.0.0.1: icmp_seq=3 ttl=64 time=0.039 ms
  64 bytes from 127.0.0.1: icmp_seq=4 ttl=64 time=0.041 ms
  ```

* **Step 2: Test Gateway Connectivity**
  Find your default gateway:
  ```bash
  ip route show default
  ```
  Ping the gateway IP discovered in the previous step (e.g., `192.168.1.1`):
  ```bash
  ping -c 4 192.168.1.1
  ```

* **Step 3: Test External Connectivity**
  ```bash
  # Test Google's public DNS
  ping -c 4 8.8.8.8

  # Test a domain name resolution
  ping -c 4 google.com
  ```

#### Subtask 1.2: Using the traceroute Command
Traceroute tracks the precise path packets take to reach a destination.

* **Step 1: Install traceroute**
  ```bash
  # For RHEL/CentOS systems
  sudo yum install -y traceroute

  # For newer RHEL/CentOS systems using dnf
  sudo dnf install -y traceroute
  ```

* **Step 2: Trace Route to External Host**
  ```bash
  traceroute google.com
  ```
  *Note: Each line represents a hop (router). The three time values show round-trip times for three test packets.*

* **Step 3: Trace Route with IP Addresses**
  ```bash
  traceroute -n 8.8.8.8
  ```
  *Note: The `-n` flag disables DNS lookups to return faster results.*

#### Subtask 1.3: Using the nslookup Command
`nslookup` is used to troubleshoot domain name resolution (DNS) records.

* **Step 1: Basic DNS Lookup**
  ```bash
  nslookup google.com
  ```

* **Step 2: Reverse DNS Lookup**
  ```bash
  nslookup 8.8.8.8
  ```

* **Step 3: Query Specific DNS Record Types**
  ```bash
  # Query MX records (mail servers)
  nslookup -type=MX google.com

  # Query NS records (name servers)
  nslookup -type=NS google.com
  ```

* **Step 4: Query via Specific DNS Server**
  ```bash
  nslookup google.com 8.8.8.8
  ```

---

### Task 2: Managing Network Configurations with nmcli

`nmcli` is the NetworkManager Command Line Interface used to control network configurations.

#### Subtask 2.1: Viewing Current Network Status
```bash
# Step 1: Check general NetworkManager status
nmcli general status

# Step 2: List all network devices
nmcli device status

# Step 3: Show detailed device information
nmcli device show
```

#### Subtask 2.2: Managing Network Connections
```bash
# Step 1: List all connections
nmcli connection show

# Step 2: Show active connections only
nmcli connection show --active

# Step 3: View detailed connection info (replace connection-name)
nmcli connection show "System eth0"
```

#### Subtask 2.3: Modifying Network Settings
* **Step 1: Change DNS Settings**
  ```bash
  sudo nmcli connection modify "System eth0" ipv4.dns "8.8.8.8,8.8.4.4"
  sudo nmcli connection up "System eth0"
  ```

* **Step 2: Set Static IP Address**
  ```bash
  sudo nmcli connection modify "System eth0" ipv4.addresses "192.168.1.100/24"
  sudo nmcli connection modify "System eth0" ipv4.gateway "192.168.1.1"
  sudo nmcli connection modify "System eth0" ipv4.method manual
  sudo nmcli connection up "System eth0"
  ```

* **Step 3: Revert Connection to DHCP**
  ```bash
  sudo nmcli connection modify "System eth0" ipv4.method auto
  sudo nmcli connection up "System eth0"
  ```

#### Subtask 2.4: Creating New Network Connections
```bash
# Step 1: Create a new Ethernet connection profile
sudo nmcli connection add type ethernet con-name "lab-connection" ifname eth0

# Step 2: Configure the new profile static parameters
sudo nmcli connection modify "lab-connection" ipv4.addresses "192.168.1.150/24"
sudo nmcli connection modify "lab-connection" ipv4.gateway "192.168.1.1"
sudo nmcli connection modify "lab-connection" ipv4.dns "8.8.8.8"
sudo nmcli connection modify "lab-connection" ipv4.method manual

# Step 3: Activate the new connection
sudo nmcli connection up "lab-connection"
```

---

### Task 3: Configuring Firewall Rules with firewalld

`firewalld` provides dynamic firewall management across different isolation zones.

#### Subtask 3.1: Understanding Firewalld Basics
```bash
# Step 1: Check status, start and enable service
sudo systemctl status firewalld
sudo systemctl start firewalld
sudo systemctl enable firewalld

# Step 2: View zone configuration info
sudo firewall-cmd --get-default-zone
sudo firewall-cmd --get-zones
sudo firewall-cmd --get-active-zones

# Step 3: View full configuration for zones
sudo firewall-cmd --list-all
sudo firewall-cmd --zone=public --list-all
```

#### Subtask 3.2: Managing Firewall Services
```bash
# Step 1: List available services
sudo firewall-cmd --get-services

# Step 2: Add core infrastructure services permanently
sudo firewall-cmd --zone=public --add-service=http --permanent
sudo firewall-cmd --zone=public --add-service=https --permanent
sudo firewall-cmd --zone=public --add-service=ssh --permanent

# Step 3: Reload configuration to apply changes
sudo firewall-cmd --reload

# Step 4: Verify current active services
sudo firewall-cmd --zone=public --list-services
```

#### Subtask 3.3: Managing Firewall Ports
```bash
# Step 1: Open specific protocol ports
sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent
sudo firewall-cmd --zone=public --add-port=53/udp --permanent
sudo firewall-cmd --zone=public --add-port=3000-3005/tcp --permanent

# Step 2: Reload and verify changes
sudo firewall-cmd --reload
sudo firewall-cmd --zone=public --list-ports

# Step 3: Remove custom ports
sudo firewall-cmd --zone=public --remove-port=8080/tcp --permanent
sudo firewall-cmd --reload
```

#### Subtask 3.4: Advanced Firewall Configuration
```bash
# Step 1: Create and define a custom system service
sudo firewall-cmd --permanent --new-service=myapp
sudo firewall-cmd --permanent --service=myapp --set-description="My Custom Application"
sudo firewall-cmd --permanent --service=myapp --set-short="MyApp"
sudo firewall-cmd --permanent --service=myapp --add-port=9090/tcp

# Step 2: Bind custom service to public zone and reload
sudo firewall-cmd --zone=public --add-service=myapp --permanent
sudo firewall-cmd --reload

# Step 3: Configure complex rich rules
# Allow a specific IP address to access SSH port
sudo firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="192.168.1.100" service name="ssh" accept' --permanent

# Block/Drop HTTP requests originating from a specific bad host IP
sudo firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="192.168.1.200" service name="http" drop' --permanent

# Reload to apply rules
sudo firewall-cmd --reload
```

---

## 📂 File Structure

Below is the directory architecture for this repository:

```text
networking-lab/
├── docs/
│   ├── Task1_Connectivity_Tools.md  # Detailed logs for ping, traceroute, nslookup
│   ├── Task2_nmcli_Management.md    # Network profile configs and steps
│   └── Task3_Firewalld_Rules.md     # Advanced firewall configurations
├── scripts/
│   ├── setup_network.sh             # Automation script for nmcli profiles
│   └── configure_firewall.sh        # Script containing firewalld rules
├── LICENSE                          # Lab distribution license file
└── README.md                        # Primary lab documentation guide
```

---

## 🏁 Conclusion

This lab provides an end-to-end framework to diagnose, configure, and secure Linux network interfaces. By combining basic connectivity checks (`ping`/`traceroute`) with profile orchestration (`nmcli`) and traffic enforcement (`firewalld`), you establish foundational skills necessary for comprehensive system administration and modern DevOps troubleshooting paths.
