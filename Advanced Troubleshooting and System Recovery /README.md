# Advanced Troubleshooting and System Recovery

A comprehensive, step-by-step hands-on lab manual for system engineers to isolate infrastructure performance bottlenecks, parse binary systemd journals, force single-user rescue environments, and automate persistent backup configurations on Linux distributions.

---

## 🎯 Objectives
By the end of this lab, you will be able to:
* Analyze aggregated system journals using `journalctl` and core text-based log structures.
* Deploy runtime troubleshooting binaries to isolate high CPU, memory, and disk space saturation.
* Drop system operational run levels securely into `systemd` single-user rescue environments.
* Debug corrupted unit target files, resolve application exit faults, and restart dependencies.
* Build automated bash shell scripts to preserve `/etc/` system configuration parameters.
* Implement native `systemd.timer` micro-schedulers to replace old cron architectures.

---

## 🧰 Diagnostic & Recovery Tools Matrix
The following table outlines the foundational system utilities used throughout this recovery guide:


| Utility Name | Primary Purpose in this Lab | Common Use Case Example |
| :--- | :--- | :--- |
| `journalctl` | Interrogates the centralized systemd binary logging engine. | `journalctl -p err` |
| `systemctl` | Controls run targets, queries failed modules, and shifts runlevels. | `systemctl rescue` |
| `tail` / `grep` | Parses legacy system text logs using regex pipe filters. | `tail -50 /var/log/messages` |
| `top` | Tracks system resource usage and isolates heavy pid processes. | `top` |
| `df` / `du` | Analyzes disk storage blocks and estimates file directory sizes. | `df -h` |
| `tar` | Packages configuration folders into compressed `.tar.gz` archives. | `tar -czf backup.tar.gz /etc` |

---

## 💻 Lab Environment & Prerequisites

### Prerequisites
* Basic competency over core Linux terminal command execution paths.
* Familiarity with `systemd` unit files, structural types, and background actions.
* Core knowledge of administrative file systems, folders, and read/write permission bitmasks.
* Structural understanding of user elevation contexts via `sudo`.

### Environment Specs
This architecture lab manual runs across systemd-compliant Enterprise Linux targets:
* **OS:** Red Hat Enterprise Linux (RHEL) / CentOS Stream (8 or 9)
* **Access:** Full administrative elevation privileges (`sudo` block controls)
* **Scenarios:** Contains broken infrastructure mock service codes designed to trigger systemic loop faults.

---

## 🚀 Lab Implementation Steps

### Task 1: Perform Advanced Troubleshooting Using Logs and System Tools

#### Subtask 1.1: Analyze System Logs with journalctl
Interrogate binary records to pinpoint exact hardware alerts or misconfigured software:

```bash
# Query the trailing 50 lines recorded across all active infrastructure paths
sudo journalctl -n 50

# Filter the system journal to isolate errors and more severe priority faults (0-3)
sudo journalctl -p err

# Limit log lookups strictly to errors emitted over the previous two hours
sudo journalctl --since "2 hours ago"

# Follow incoming kernel and application system outputs in real-time
sudo journalctl -f
# Note: Send terminal a Ctrl+C escape sequence to terminate log streams
```

#### Subtask 1.2: Examine Traditional Log Files
Inspect historical persistent plain-text flat logs maintained within the legacy syslog directory tree:

```bash
# Enter the system log directory and list active files
cd /var/log
ls -la

# Audit the general system message repository log file
sudo tail -50 /var/log/messages

# Review the security and user access validation tracking metrics file
sudo tail -30 /var/log/secure

# Pipe system logs through an case-insensitive regex to catch system failures
sudo grep -i "error\|fail\|critical" /var/log/messages | tail -20
```

#### Subtask 1.3: Use System Diagnostic Tools
Perform direct performance tuning and check hardware limitations to detect compute saturation:

```bash
# Audit interactive task managers to map CPU/Memory spikes (Press 'q' to quit)
top

# Check volume partition allocations and disk layer saturation levels
df -h

# Identify which directory branch inside the log paths uses the most space
du -sh /var/log/*

# Query runtime host tracking variables, load averages, and active users
uptime
w

# Test active network interface routing and trace active listening server sockets
ping -c 4 8.8.8.8
netstat -tuln | head -20

# Extract hardware profiles including CPU structures, memory tables, and block architectures
lscpu
free -h
lsblk
```

---

### Task 2: Recover from System Failure Using systemd and Rescue Mode

#### Subtask 2.1: Simulate a System Issue
Create a faulty multi-user target background configuration to track loop failures:

```bash
# Write an unoptimized loop configuration script directly into systemd unit directories
sudo tee /etc/systemd/system/problem-service.service > /dev/null << 'EOF'
[Unit]
Description=Problem Service for Testing
After=network.target

[Service]
Type=simple
ExecStart=/bin/bash -c "while true; do echo 'Service running'; sleep 30; done"
ExecStop=/bin/bash -c "exit 1"
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF

# Force systemd to register the service modifications and start the loop task
sudo systemctl daemon-reload
sudo systemctl enable problem-service.service
sudo systemctl start problem-service.service

# Audit status outputs to see state transitions and crash metrics
sudo systemctl status problem-service.service
```

#### Subtask 2.2: Access Rescue Mode
Shift operational system run targets to simulate emergency offline troubleshooting states:

```bash
# Switch the active operating engine target down into single-user rescue mode
sudo systemctl rescue

# Identify the system's current base boot target path configuration
systemctl get-default
systemctl list-units --type=target

# Review the trimmed, minimized footprint of active processes allowed inside the rescue environment
systemctl list-units --state=active
```

#### Subtask 2.3: Diagnose Issues in Rescue Mode
Isolate the configuration anomalies while working inside the restricted rescue target:

```bash
# Query the system to isolate explicitly broken or crashed services
systemctl --failed

# Deep dive into the error logs of the target failing service unit
systemctl status problem-service.service
journalctl -u problem-service.service -n 20

# Graph out dependencies to trace service prerequisite paths
systemctl list-dependencies rescue.target
```

---

### Task 3: Restore System Services and Configurations

#### Subtask 3.1: Fix Service Configuration Issues
Tear down the problematic execution configurations and declare clean shutdown signals:

```bash
# Step 1: Force stop the errant loop process execution paths
sudo systemctl stop problem-service.service

# Step 2: Overwrite the service manifest with clean termination logic
sudo tee /etc/systemd/system/problem-service.service > /dev/null << 'EOF'
[Unit]
Description=Fixed Problem Service
After=network.target

[Service]
Type=simple
ExecStart=/bin/bash -c "while true; do echo 'Service running properly'; sleep 30; done"
ExecStop=/bin/kill -TERM $MAINPID
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF

# Step 3: Refresh internal dependencies and restart the service infrastructure
sudo systemctl daemon-reload
sudo systemctl start problem-service.service

# Step 4: Verify the service returns a stable running condition state
sudo systemctl status problem-service.service
```

#### Subtask 3.2: Restore System to Normal Operation
Return your infrastructure boundaries to standard user operations and verify connectivity:

```bash
# Step 1: Shift run targets back up to standard multi-user system operations
sudo systemctl default

# Step 2: Confirm essential base services (SSH, Networking) returned to normal
systemctl list-units --type=service --state=active | grep -E "(ssh|network|systemd)"

# Step 3: Review critical logs generated over the last 10 minutes to verify clean transitions
sudo journalctl -p warning --since "10 minutes ago"
```

#### Subtask 3.3: Implement System Recovery Best Practices
Build automated configuration backups to guard your critical infrastructure files:

```bash
# Step 1: Build a robust, rotating configuration backup archiving shell script
sudo tee /usr/local/bin/backup-configs.sh > /dev/null << 'EOF'
#!/bin/bash
BACKUP_DIR="/var/backups/system-configs"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"

tar -czf "$BACKUP_DIR/system-configs-$DATE.tar.gz" \
    /etc/systemd/system/ \
    /etc/fstab \
    /etc/hosts \
    /etc/resolv.conf \
    /etc/ssh/sshd_config \
    2>/dev/null

echo "System configuration backup completed: $BACKUP_DIR/system-configs-$DATE.tar.gz"

cd "$BACKUP_DIR"
ls -t system-configs-*.tar.gz | tail -n +6 | xargs -r rm
echo "Backup cleanup completed"
EOF

# Step 2: Set file execution permissions on the backup utility script
sudo chmod +x /usr/local/bin/backup-configs.sh

# Step 3: Run a manual script validation block test
sudo /usr/local/bin/backup-configs.sh

# Step 4: Provision a matching systemd oneshot automation service wrapper
sudo tee /etc/systemd/system/backup-configs.service > /dev/null << 'EOF'
[Unit]
Description=System Configuration Backup
Wants=backup-configs.timer

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup-configs.sh

[Install]
WantedBy=multi-user.target
EOF

# Step 5: Construct the calendar timer engine to trigger the execution routine daily
sudo tee /etc/systemd/system/backup-configs.timer > /dev/null << 'EOF'
[Unit]
Description=Run backup-configs daily
Requires=backup-configs.service

[Timer]
OnCalendar=daily
Persistent=true

[Install]
WantedBy=timers.target
EOF

# Step 6: Enable and initiate your new native calendar system automation timer
sudo systemctl daemon-reload
sudo systemctl enable backup-configs.timer
sudo systemctl start backup-configs.timer
sudo systemctl status backup-configs.timer
```

#### Subtask 3.4: Verify System Recovery
Complete a rigorous sanity check across all layers to ensure host stability:

```bash
# Step 1: Review operational metrics across essential network system daemons
sudo systemctl status sshd
sudo systemctl status NetworkManager
sudo systemctl status problem-service.service

# Step 2: Ensure no critical error indicators appear in the recent logs
sudo journalctl -p err --since "30 minutes ago"

# Step 3: Run comprehensive networking, storage, and application restarts
ping -c 3 google.com
df -h
sudo systemctl restart problem-service.service
sudo systemctl status problem-service.service

# Step 4: Generate a standardized historical system recovery log documentation sheet
sudo tee /var/log/recovery-log-$(date +%Y%m%d).txt > /dev/null << EOF
System Recovery Log - $(date)
================================

Issues Identified:
- Problematic service configuration causing restart loops
- Service dependencies not properly configured

Actions Taken:
1. Accessed rescue mode for system isolation
2. Overwrote broken unit directives with safe SIGTERM handling
3. Initialized automated configuration backup scripts and systemd timers
EOF
```

---

## 🛠️ Troubleshooting Emergency Scenarios

### Scenario 1: `systemctl rescue` fails or requests root user lockout credentials
* **Cause:** The local root account is locked or lacks a defined password inside cloud-provisioned base configurations.
* **Resolution:** Run `sudo passwd root` from a standard shell to explicitly set up localized credential bypass arguments before transitioning into single-user environments.

### Scenario 2: Systemd configuration timer fails to run or exhibits an inactive status
* **Cause:** The accompanying automated service block context files fail validation parsing, or the system timer lacks a correct matching path pointer.
* **Resolution:** Run `sudo systemd-analyze verify /etc/systemd/system/backup-configs.*` to target format errors. Ensure you executed `sudo systemctl daemon-reload` after editing the service configurations.

---

## 🏁 Conclusion
By completing this advanced system lab, you have evolved from simple black-box terminal scripting into orchestrating programmatic target level management loops. 

Key architectural methodologies mastered include:
* **Forensic Filtering Matrix:** Combining time constraints, systemd process identifiers, and log level masks to track down cascading service loop drop faults.
* **Operational Mode Isolation:** Shifting runtime scopes into recovery configurations to troubleshoot complex configuration conflicts safely.
* **Self-Healing Automations:** Designing robust environment wrappers with `on-failure` parameters to prevent application memory leaks and loop failures.
* **Modern Task Schedulers:** Moving away from traditional cron files by engineering resilient, native `systemd.timer` architectures that track cross-boot execution variables automatically.

---

## 📁 Repository Directory File Structure
To organize your configuration variables and testing scripts within this repository, maintain the layout illustrated in the schema map below:

```text
📁 system-recovery-lab/
├── 📄 README.md                        # Main infrastructure lab orchestration guide
├── 📁 automation/                      # Core automation and recovery utility assets
│   └── ⚙️ backup-configs.sh            # Dynamic rotation backup automation script
└── 📁 systemd-units/                   # Custom configuration service block files
    ├── 📄 backup-configs.service       # Automated execution daemon setup schema
    ├── 📄 backup-configs.timer         # Native calendar scheduling configuration definitions
    └── 📄 problem-service.service      # Repaired application execution service unit files
```
