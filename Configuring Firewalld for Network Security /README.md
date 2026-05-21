# Configuring Firewalld for Network Security

A comprehensive, step-by-step hands-on lab manual for installing, configuring, and testing `firewalld` to secure Linux systems. This guide covers basic rule management, advanced rich rules, and multi-zone network configurations.

---

## 🎯 Objectives
By the end of this lab, you will be able to:
* Understand `firewalld` fundamentals and its role in Linux network security.
* Install and manage the `firewalld` service on systemd-based Linux environments.
* Implement runtime and permanent firewall rules using `firewall-cmd`.
* Create custom firewall zones and assign physical network interfaces.
* Manage services, specific ports, and port ranges across discrete zones.
* Test active configurations using tools like `nmap`, `telnet`, and `netcat`.
* Write secure communication policies with advanced Rich Rules.
* Troubleshoot everyday firewall misconfigurations.

---

## 💻 Lab Environment & Prerequisites

### Prerequisites
* Basic familiarity with the Linux Command Line Interface (CLI).
* Familiarity with networking core concepts (IP addresses, subnets, ports, protocols).
* Basic knowledge of TCP/IP and common network services (SSH, HTTP, HTTPS, FTP).

### Environment Specs
This lab is designed to run natively on **CentOS / RHEL 8 or 9** platforms with root or `sudo` level privileges.
* **OS:** Red Hat Enterprise Linux / CentOS Stream
* **Package Manager:** `dnf`
* **Interfaces Used:** Local Loopback, Network Interface Cards (e.g., `eth0`)

---

## 🚀 Lab Implementation Steps

### Task 1: Create Firewall Rules Using Firewalld

#### Subtask 1.1: Install and Start Firewalld Service
Ensure `firewalld` is installed and actively running on the system:

```bash
# Check if firewalld package is installed
rpm -qa | grep firewalld

# Install firewalld if missing
sudo dnf install firewalld -y

# Start and enable the daemon on system boot
sudo systemctl start firewalld
sudo systemctl enable firewalld

# Verify running state
sudo systemctl status firewalld
sudo firewall-cmd --state
```

#### Subtask 1.2: Understanding Firewalld Basics
Gather information about your default zones and current runtime configurations:

```bash
# Inspect the active zone's configuration details
sudo firewall-cmd --list-all

# Enumerate all system-provided zones
sudo firewall-cmd --get-zones

# Find out which zone is marked as default
sudo firewall-cmd --get-default-zone

# Identify zones currently bound to active interfaces or sources
sudo firewall-cmd --get-active-zones
```

#### Subtask 1.3: Create Basic Firewall Rules
Manage standard network applications and network transport layer ports:

```bash
# Allow HTTP traffic temporarily (wiped on reload/restart)
sudo firewall-cmd --add-service=http

# Allow HTTP and HTTPS permanently
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --add-service=https --permanent

# Allow a dedicated custom port (TCP 8080) permanently
sudo firewall-cmd --add-port=8080/tcp --permanent

# Allow a sequential range of ports (TCP 3000 to 3005) permanently
sudo firewall-cmd --add-port=3000-3005/tcp --permanent

# Reload configurations to commit permanent changes to runtime
sudo firewall-cmd --reload

# Confirm the new parameters are active
sudo firewall-cmd --list-all
```

#### Subtask 1.4: Create Advanced Firewall Rules
Isolate traffic or bind access based on discrete IP addresses or network spaces using rich rules:

```bash
# Whitelist a single host IP address permanently
sudo firewall-cmd --add-source=192.168.1.100 --permanent

# Whitelist an entire local network subnet
sudo firewall-cmd --add-source=192.168.1.0/24 --permanent

# Explicitly drop traffic originating from an untrusted host using a rich rule
sudo firewall-cmd --add-rich-rule='rule source address="192.168.1.50" drop' --permanent

# Allow SSH access exclusively for a specific trusted management IP
sudo firewall-cmd --add-rich-rule='rule source address="192.168.1.100" service name="ssh" accept' --permanent

# Apply updates and verify
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

---

### Task 2: Test Firewall Configuration

#### Subtask 2.1: Install Testing Tools
Set up diagnostic utilities alongside a test web application environment:

```bash
# Install network discovery and testing binaries
sudo dnf install nmap telnet nc -y

# Setup and deploy the Apache HTTP Server
sudo dnf install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd

# Build a basic web payload file
echo "<h1>Firewall Test Page</h1>" | sudo tee /var/www/html/index.html
```

#### Subtask 2.2: Test HTTP/HTTPS Access
Validate path accessibility across allowed ports:

```bash
# Test local response
curl http://localhost

# Test from an external host using your system IP
curl http://[YOUR_SERVER_IP]

# Scan local port 80 to verify state
nmap -p 80 localhost

# Query custom port 8080 via telnet
telnet localhost 8080
```

#### Subtask 2.3: Test SSH Access
Ensure host management access remains open and operational:

```bash
# Check if ssh service string exists inside active rules
sudo firewall-cmd --list-services | grep ssh

# Verify local socket capability
ssh localhost

# Check accessibility via Nmap
nmap -p 22 localhost
```

#### Subtask 2.4: Test Blocked Ports
Confirm that restricted services are dropping unauthorized connections cleanly:

```bash
# Attempt a remote connection to a blocked database engine port
telnet localhost 3306

# Run an open-port signature scan across the engine host
nmap localhost

# Test explicit communication drops or timeout patterns
nc -zv localhost 3306
```

---

### Task 3: Set Up Zones and Services for Secure Communication

#### Subtask 3.1: Understanding and Managing Zones
Audit existing zones and provision a custom boundary zone for application instances:

```bash
# Review detailed parameters for all available zones
sudo firewall-cmd --list-all-zones

# Target specific baseline zones for inspection
sudo firewall-cmd --zone=public --list-all
sudo firewall-cmd --zone=internal --list-all
sudo firewall-cmd --zone=dmz --list-all

# Instantiate a clean, custom zone for a web server profile
sudo firewall-cmd --new-zone=webserver --permanent
sudo firewall-cmd --reload

# Validate existence of the new zone profile
sudo firewall-cmd --get-zones
```

#### Subtask 3.2: Configure Custom Zone
Shape the custom zone by bundling specific requirements:

```bash
# Append web and management protocol signatures to the zone profile
sudo firewall-cmd --zone=webserver --add-service=http --permanent
sudo firewall-cmd --zone=webserver --add-service=https --permanent
sudo firewall-cmd --zone=webserver --add-service=ssh --permanent

# Open specialized ports inside the target zone scope
sudo firewall-cmd --zone=webserver --add-port=8080/tcp --permanent

# Define clear documentation metadata for the zone configuration
sudo firewall-cmd --zone=webserver --set-description="Web Server Zone for HTTP/HTTPS traffic" --permanent

# Reload infrastructure and verify properties
sudo firewall-cmd --reload
sudo firewall-cmd --zone=webserver --list-all
```

#### Subtask 3.3: Assign Network Interfaces to Zones
Bound logical physical hardware cards into your newly customized boundary zones:

```bash
# Enumerate interface adapter layouts (e.g., eth0, ens3)
ip addr show

# Determine interface zone affinity
sudo firewall-cmd --get-active-zones

# Shift chosen interface hardware into your webserver zone
sudo firewall-cmd --zone=webserver --change-interface=eth0 --permanent

# Validate zone re-assignment
sudo firewall-cmd --get-active-zones
```

#### Subtask 3.4: Configure Internal Zone for Secure Communication
Isolate system management networks from the public-facing DMZ:

```bash
# Allow critical infrastructure sharing parameters within the internal zone
sudo firewall-cmd --zone=internal --add-service=ssh --permanent
sudo firewall-cmd --zone=internal --add-service=samba --permanent
sudo firewall-cmd --zone=internal --add-service=nfs --permanent

# Map explicit private infrastructure subnets to the internal zone map
sudo firewall-cmd --zone=internal --add-source=192.168.1.0/24 --permanent
sudo firewall-cmd --zone=internal --add-source=10.0.0.0/8 --permanent

# Limit a separate semi-trusted perimeter DMZ to front-facing services
sudo firewall-cmd --zone=dmz --add-service=http --permanent
sudo firewall-cmd --zone=dmz --add-service=https --permanent
sudo firewall-cmd --zone=dmz --add-port=8080/tcp --permanent

# Refresh runtime tables and inspect compliance records
sudo firewall-cmd --reload
sudo firewall-cmd --zone=internal --list-all
sudo firewall-cmd --zone=dmz --list-all
```

#### Subtask 3.5: Create Rich Rules for Advanced Security
Add fine-grained access control lists (ACLs), protection flags, and event logging parameters:

```bash
# Limit SSH management visibility strictly to a trusted administration segment
sudo firewall-cmd --add-rich-rule='rule source address="192.168.1.0/24" service name="ssh" accept' --permanent

# Build zero-trust containment: Block everything inside the internal zone except specific targets
sudo firewall-cmd --zone=internal --add-rich-rule='rule source address="0.0.0.0/0" drop' --permanent
sudo firewall-cmd --zone=internal --add-rich-rule='rule source address="192.168.1.0/24" accept' --permanent

# Implement an SSH brute-force defense limit (Maximum 3 connection attempts per minute)
sudo firewall-cmd --add-rich-rule='rule service name="ssh" accept limit value="3/m"' --permanent

# Route all dropped frames directly into your system kernel ring logs
sudo firewall-cmd --add-rich-rule='rule drop log prefix="FIREWALL-DROP: " level="warning"' --permanent

# Apply rules and evaluate rich parameters
sudo firewall-cmd --reload
sudo firewall-cmd --list-rich-rules
```

#### Subtask 3.6: Test Zone-Based Security
Verify isolation properties across configurations:

```bash
# Inspect services across contrasting public/internal profiles
sudo firewall-cmd --zone=public --list-services
sudo firewall-cmd --zone=internal --list-services

# Audit local listening socket accessibility profiles
nmap -p 22,80,443 localhost

# Follow real-time system messages and dropped packet events
sudo journalctl -u firewalld -f

# Verify total interface routing properties match intentions
sudo firewall-cmd --get-active-zones
sudo firewall-cmd --list-all-zones | grep -A 10 "webserver"
```

---

## 🛠️ Troubleshooting Common Issues

### Issue: Changes disappear after reloading or rebooting
* **Cause:** Rules were applied directly to the current runtime engine context without utilizing the critical `--permanent` attribute modifier.
* **Resolution:** Re-execute your custom parameters ensuring you trail your lines with the explicit `--permanent` option, then execute `sudo firewall-cmd --reload`.

### Issue: Network interfaces disappear from their assigned zones after restarting the service
* **Cause:** Network Manager may be overriding active interfaces, or configurations were modified purely inside temporary runtime mappings.
* **Resolution:** Ensure the flag parameter `--permanent` accompanies your `--change-interface` configuration executions. Additionally, you can add `ZONE="your-zone"` within your system network setup files (e.g., `/etc/sysconfig/network-scripts/ifcfg-interface`).

### Issue: System services or specific custom ports are completely unreachable externally
* **Cause:** The network interfaces may not be assigned to the appropriate zone, or the default zone configuration is dropping the traffic.
* **Resolution:** Verify which zone is handling the interface traffic using `sudo firewall-cmd --get-active-zones`. Ensure that specific zone has explicitly permitted the necessary services or port definitions.

---

## 🏁 Conclusion
By completing this practical lab, you have moved from basic service filtering to managing a multi-tiered, zone-based network architecture using `firewalld`. 

Key takeaways from this exercise include:
* **Runtime vs. Permanent State:** Understanding that modifications must use the `--permanent` flag to persist across reloads.
* **Network Segmentation:** Isolating web services (`dmz` and `webserver` zones) from local management systems (`internal` zone) to minimize the attack surface.
* **Granular Traffic Control:** Leveraging advanced Rich Rules to limit connection rates, log anomalies, and lock down management access like SSH to authorized subnets.
* **Active Validation:** Utilizing industry-standard utilities (`nmap`, `netcat`, and `journalctl`) to audit your filtering choices and verify network compliance.

Maintaining restrictive, zone-based access policies remains a foundational component of modern infrastructure security and Zero-Trust methodologies.
