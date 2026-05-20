# Configuring and Securing SSH

A comprehensive lab guide designed to teach secure remote systems administration using OpenSSH. You will learn to install services, customize server definitions, deploy cryptographic keys, harden access vectors, and fix standard authentication issues.

---

## 🎯 Objectives
By the end of this lab, you will be able to:
* **Install and configure** OpenSSH server securely on a Linux platform.
* **Understand** critical SSH service configurations (`sshd_config`) and default system parameters.
* **Test connectivity matrices** across both internal loopbacks and external host machines.
* **Implement key-based authentication** mechanisms to mitigate brute-force risks.
* **Apply defense-in-depth security** policies by restricting administrative vulnerabilities.
* **Isolate and troubleshoot** transport-layer failures and cryptographic handshake drops.

---

## 🛠️ Prerequisites & Lab Environment

### Prerequisites
Before starting, ensure you have:
* Familiarity with Linux command-line operations and basic text editors (`nano` or `vim`).
* Understanding of basic networking properties (IP mappings, interface bounds, TCP ports).
* Conceptual awareness of Linux user identities, file access controls, and home paths.

### Lab Environment
This lab is optimized for **Al Nafi Cloud Machines** (CentOS/RHEL 8 or 9). The landscape delivers:
* **Two interconnected virtual hosts** (Server and Client instances).
* Full non-restrictive `sudo` processing rights.
* Pre-configured local package repositories for rapid deployment.

---

## 🚀 Lab Tasks

### Task 1: Install and Configure OpenSSH Server

#### 1.1 Check Current SSH Status
Inspect your local environment to verify if the OpenSSH server daemon is available:
```bash
# Query installed packages
rpm -qa | grep openssh-server

# Check active service runtime status
systemctl status sshd

# Install the server daemon if missing
sudo dnf install openssh-server -y
```

#### 1.2 Start and Enable SSH Service
Ensure the daemon remains active across unpredictable system initialization states:
```bash
sudo systemctl start sshd
sudo systemctl enable sshd
sudo systemctl status sshd
```

#### 1.3 Configure SSH Server Settings
```bash
# Back up baseline settings before editing
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup

# Open the configuration file
sudo nano /etc/ssh/sshd_config
```

Examine, uncomment, and enforce the security targets listed below:
```text
Port 22
PermitRootLogin no
PubkeyAuthentication yes
PasswordAuthentication yes
PermitEmptyPasswords no
MaxAuthTries 3
ClientAliveInterval 300
ClientAliveCountMax 2
```

Validate and reload your modifications:
```bash
# Dry-run test the syntax configuration for errors
sudo sshd -t

# If clean, restart the daemon loop
sudo systemctl restart sshd
```

#### 1.4 Configure Firewall for SSH
Punch holes dynamically through the default kernel packet filtering systems:
```bash
# Verify current operational firewall state
sudo firewall-cmd --state

# Expose default SSH rules permanently
sudo firewall-cmd --permanent --add-service=ssh

# Note: If a custom network port was set in step 1.3, expose it manually:
# sudo firewall-cmd --permanent --add-port=YOUR_PORT/tcp

# Commit operational rules down to live memory
sudo firewall-cmd --reload
sudo firewall-cmd --list-services
```

---

### Task 2: Test SSH Connection Locally and Remotely

#### 2.1 Test Local SSH Connection
```bash
# Provision a test target profile
sudo useradd testuser
sudo passwd testuser

# Execute loopback handshake verification
ssh testuser@localhost
```
*Note: Accept the cryptographic fingerprint validation when prompted (`yes`), then submit the user password.*

Verify and drop out of the active loopback socket:
```bash
whoami
hostname
exit
```

#### 2.2 Test Remote SSH Connection
```bash
# Determine local networking interface address
ip addr show
```
Move over to your second client shell workspace and log in using the server's network IP:
```bash
ssh testuser@YOUR_IP_ADDRESS

# Use debugging flags if the handshake hangs
ssh -v testuser@YOUR_IP_ADDRESS
```

#### 2.3 Monitor SSH Connections
Keep an eye on system connection metrics using operational audit streams:
```bash
# Review open network sockets matching the service port
sudo ss -tuln | grep :22

# Tail live service logging events
sudo journalctl -u sshd -f

# Interrogate structural authentication streams
sudo tail -f /var/log/secure
```

---

### Task 3: Set Up Key-Based Authentication

#### 3.1 Generate SSH Key Pair
Execute this generation task explicitly from your **client machine system terminal**:
```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
*Note: Press **Enter** to default the path locations (`~/.ssh/id_rsa`). Applying an optional security passphrase is recommended.*

```bash
# Inspect the newly generated public and private key metadata
ls -la ~/.ssh/
cat ~/.ssh/id_rsa.pub
```

#### 3.2 Copy Public Key to Remote Server
Automate public key placement or perform manual insertion procedures:
```bash
# Option A: Automated delivery (Recommended)
ssh-copy-id testuser@YOUR_IP_ADDRESS

# Option B: Manual injection pipeline alternative
# cat ~/.ssh/id_rsa.pub | ssh testuser@YOUR_IP_ADDRESS "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

Lock down access control rules over home files directly on the target host server:
```bash
ssh testuser@YOUR_IP_ADDRESS "chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys"
```

#### 3.3 Test Key-Based Authentication
Verify passwordless authentication:
```bash
ssh testuser@YOUR_IP_ADDRESS
```
Validate key utilization properties over remote logging frameworks:
```bash
sudo journalctl -u sshd | grep "Accepted publickey"
```

#### 3.4 Disable Password Authentication
Once key delivery passes validation checks, deprecate legacy authentication mechanisms entirely:
```bash
sudo nano /etc/ssh/sshd_config
```
```text
PasswordAuthentication no
```
```bash
# Test structural syntax integrity and restart daemon
sudo sshd -t
sudo systemctl restart sshd
```

#### 3.5 Advanced SSH Security Configuration
Harden configurations against sophisticated network exploration tasks:
```bash
sudo nano /etc/ssh/sshd_config
```
```text
Protocol 2
AllowUsers testuser
X11Forwarding no
AllowAgentForwarding no
AllowTcpForwarding no
Ciphers aes256-ctr,aes192-ctr,aes128-ctr
MACs hmac-sha2-256,hmac-sha2-512
LoginGraceTime 30
```
```bash
sudo systemctl restart sshd
```

---

## 🛠️ Troubleshooting

### Connection Refused Errors
* Ensure the server daemon remains up: `sudo systemctl status sshd`.
* Confirm local packet filters permit delivery rules: `sudo firewall-cmd --list-services`.
* Test config formatting for invalid options: `sudo sshd -t`.

### Permission Denied Errors
* Confirm local key permissions conform strictly to security baselines: `ls -la ~/.ssh/`.
* Confirm remote authority files preserve safe bit masks (`600`): `ls -la ~/.ssh/authorized_keys`.
* Pull downstream event context blocks using explicit tracking commands: `sudo journalctl -u sshd -n 20`.

### Key Authentication Not Working
* Confirm your public key string sits cleanly on a single line within the server file: `ssh testuser@YOUR_IP_ADDRESS "cat ~/.ssh/authorized_keys"`.
* Enforce context synchronization resets over Mandatory Access Controls if SELinux blocks your home paths:
  ```bash
  sudo restorecon -R -v ~/.ssh/
  ```

---

## 🔍 Verification Commands
```bash
# Audit deployment runtime configurations
sudo systemctl status sshd
sudo sshd -t
sudo ss -tuln | grep :22
sudo journalctl -u sshd -n 10
ssh -T testuser@localhost
```

---

## 🛡️ Best Practices
* **Principle of Least Privilege:** Use the `AllowUsers` directive to restrict server entry exclusively to explicitly defined administrator profiles.
* **Rotate Keys Periodically:** Treat SSH keys like long-lived passwords. Schedule regular rotations and revoke access immediately when team members offboard.
* **Enforce SElinux Compliance:** Keep SELinux in `Enforcing` mode. Use standard file creation commands to prevent permission corruption on access files.
