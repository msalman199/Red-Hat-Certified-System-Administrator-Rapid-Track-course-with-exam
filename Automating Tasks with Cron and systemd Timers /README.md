# 🛠️ Automating Tasks with Cron and systemd Timers

This repository contains the complete documentation, script configurations, and file tree structure for the **Automating Tasks with Cron and systemd Timers** lab.

---

## 🎯 Objectives
By the end of this lab, you will be able to:
* 🔹 Understand the structural differences between cron and systemd timers.
* 🔹 Create and configure robust cron jobs using standard crontab execution syntax.
* 🔹 Set up systemd timers alongside standard oneshot service units.
* 🔹 Test, verify, track, and debug automated automation targets safely.
* 🔹 Apply industry best practices for cron logging and clean storage retention.

## 📋 Prerequisites
Before starting this lab, ensure you have:
* 🔹 Basic Linux command-line knowledge and terminal text editor familiarity (`nano`/`vim`).
* 🔹 Understanding of standard file permission bits (`chmod`, `chown`).
* 🔹 Basic structural knowledge of Bash scripting and runtime variables.
* 🔹 Access to a Linux node (CentOS/RHEL 8/9 preferred) with root/sudo capabilities.

---

## 🚀 Lab Tasks

### 📂 Task 1: Understanding and Creating Cron Jobs

#### 🔍 Subtask 1.1 & 1.2: Cron Environment Check & Syntax
```bash
# Verify the cron daemon is up and active
sudo systemctl status crond

# Fallback: Boot and enable the scheduler service if it's inactive
sudo systemctl start crond
sudo systemctl enable crond

# List out existing scheduled jobs for your current context user
crontab -l

# Review foundational architecture settings & system-wide automation templates
ls -la /etc/cron*
cat /etc/crontab
```

#### 🔍 Subtask 1.3: Creating Your First Cron Job
```bash
# Instantiate directories and generate the hardware status tracking script
mkdir -p ~/scripts
cat > ~/scripts/system_info.sh << 'EOF'
#!/bin/bash
echo "=== System Information Report ===" >> ~/system_reports.log
echo "Date: $(date)" >> ~/system_reports.log
echo "Uptime: $(uptime)" >> ~/system_reports.log
echo "Disk Usage:" >> ~/system_reports.log
df -h >> ~/system_reports.log
echo "Memory Usage:" >> ~/system_reports.log
free -h >> ~/system_reports.log
echo "=================================" >> ~/system_reports.log
echo "" >> ~/system_reports.log
EOF

# Grant absolute execution capability permissions to your asset file
chmod +x ~/scripts/system_info.sh

# Run a dry test execution manually to ensure file integrity checks pass
~/scripts/system_info.sh
cat ~/system_reports.log

# Append task parameters to your cron pool via interactive menu configuration
crontab -e
# [Action Required] Paste the syntax line below into the editor file:
# */5 * * * * /home/YOUR_USERNAME/scripts/system_info.sh
```

#### 🔍 Subtask 1.4 & 1.5: Complex Cron Actions & Advanced Log Capture
```bash
# Deploy an advanced daily file archiving and backup array logic model
cat > ~/scripts/daily_backup.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="/tmp/backups"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p $BACKUP_DIR
tar -czf $BACKUP_DIR/home_backup_$DATE.tar.gz ~/scripts ~/system_reports.log 2>/dev/null
find $BACKUP_DIR -name "home_backup_*.tar.gz" -mtime +7 -delete
echo "Backup completed at $(date)" >> ~/backup.log
EOF
chmod +x ~/scripts/daily_backup.sh

# Deploy a health screening and alerting logging configuration script
cat > ~/scripts/log_monitor.sh << 'EOF'
#!/bin/bash
LOG_FILE=~/cron_monitor.log
{
    echo "=== Log Monitor Started at $(date) ==="
    LOAD=$(uptime | awk -F'load average:' '{print $2}' | awk '{print $1}' | sed 's/,//')
    echo "Current system load: $LOAD"
    DISK_USAGE=$(df / | tail -1 | awk '{print $5}' | sed 's/%//')
    echo "Root disk usage: $DISK_USAGE%"
    [ $DISK_USAGE -gt 80 ] && echo "WARNING: Disk usage is above 80%"
    echo "=== Log Monitor Completed at $(date) ==="
    echo ""
} >> $LOG_FILE 2>&1
EOF
chmod +x ~/scripts/log_monitor.sh

# Open crontab to consolidate all user-level entries
crontab -e
```
**Consolidated User Crontab Entry Manifest:**
```text
# System information status check every 5 minutes
*/5 * * * * /home/YOUR_USERNAME/scripts/system_info.sh

# Nightly complete resource archive backup routine execution at 2:30 AM
30 2 * * * /home/YOUR_USERNAME/scripts/daily_backup.sh

# Weekly recursive file deletion rule targeting old /tmp garbage on Sundays at 3:00 AM
0 3 * * 0 find /tmp -name "*.tmp" -mtime +7 -delete

# Operational warning resource health tracker monitoring script every 10 minutes
*/10 * * * * /home/YOUR_USERNAME/scripts/log_monitor.sh
```

---

### ⚙️ Task 2: Setting Up systemd Timers

#### ⚙️ Subtask 2.1 & 2.2: Crafting System Services & Timers
```bash
# Review active, running, or dead core native timers managed on your local node
systemctl list-timers
ls -la /etc/systemd/system/*.timer /usr/lib/systemd/system/*.timer

# Structure a standard Oneshot execution configuration wrapper service definition
sudo tee /etc/systemd/system/system-status.service << 'EOF'
[Unit]
Description=System Status Reporter
Wants=system-status.timer

[Service]
Type=oneshot
User=root
ExecStart=/usr/local/bin/system-status.sh

[Install]
WantedBy=multi-user.target
EOF

# Inject the automation script target payload down into root binary spaces
sudo tee /usr/local/bin/system-status.sh << 'EOF'
#!/bin/bash
LOG_FILE="/var/log/system-status.log"
{
    echo "=== System Status Report - \$(date) ==="
    echo "Hostname: \$(hostname)"
    echo "Kernel: \$(uname -r)"
    echo "Load Average: \$(cat /proc/loadavg)"
    echo "Memory Info:"
    free -h
    echo "Top 5 Processes by CPU:"
    ps aux --sort=-%cpu | head -6
    echo "================================="
    echo ""
} >> \$LOG_FILE
EOF
sudo chmod +x /usr/local/bin/system-status.sh

# Implement the actual systemd Calendar structure tracking unit map
sudo tee /etc/systemd/system/system-status.timer << 'EOF'
[Unit]
Description=Run system-status.service every 15 minutes
Requires=system-status.service

[Timer]
OnCalendar=*:0/15
Persistent=true

[Install]
WantedBy=timers.target
EOF
```

#### ⚙️ Subtask 2.3 & 2.4: Instantiating and Expanding System Timers
```bash
# Alert systemd layer to register newly declared unit configuration maps
sudo systemctl daemon-reload

# Activate, fire up, and trace the operation parameters of your timer element
sudo systemctl enable system-status.timer
sudo systemctl start system-status.timer
sudo systemctl status system-status.timer

# Confirm synchronization patterns across system timers
systemctl list-timers --all
systemctl list-timers system-status.timer
```

To configure advanced cleanup functionality, implement the **Advanced Cleanup Task**:
```bash
# Setup the secondary maintenance background cleaning wrapper module
sudo tee /etc/systemd/system/temp-cleanup.service << 'EOF'
[Unit]
Description=Temporary Files Cleanup Service
After=multi-user.target

[Service]
Type=oneshot
ExecStart=/usr/local/bin/temp-cleanup.sh
User=root
EOF

# Build a binary utility execution logic script file mapping rule
sudo tee /usr/local/bin/temp-cleanup.sh << 'EOF'
#!/bin/bash
LOG_FILE="/var/log/temp-cleanup.log"
{
    echo "=== Temp Cleanup Started - \$(date) ==="
    DELETED_COUNT=\$(find /tmp -type f -mtime +7 -delete -print | wc -l)
    echo "Deleted \$DELETED_COUNT files from /tmp"
    find /var/log -name "*.log" -mtime +30 -size +100M -exec truncate -s 0 {} \;
    echo "Truncated large old log files"
    if command -v dnf &> /dev/null; then
        dnf clean packages -q
        echo "Cleaned DNF package cache"
    elif command -v yum &> /dev/null; then
        yum clean packages -q
        echo "Cleaned YUM package cache"
    fi
    echo "=== Temp Cleanup Completed - \$(date) ==="
    echo ""
} >> \$LOG_FILE
EOF
sudo chmod +x /usr/local/bin/temp-cleanup.sh
```

---

## 🔍 Troubleshooting Tips

### Common Automation Issues and Fixes

* ❌ **Issue 1: Cron script execution fails silently with no errors produced**
  * ✔️ **Solution**: User variables like `$USER`, `$HOME`, or customized paths inside standard binary strings (`/usr/local/bin`) may not load correctly inside the default clean environment shell. Always provide **explicit absolute tool locations** inside instructions (e.g., use `/usr/bin/tar` rather than `tar`).

* ❌ **Issue 2: Custom modifications inside systemd timer units are not triggering changes**
  * ✔️ **Solution**: The core system daemon holds initialization instructions inside memory cache configurations natively. Always force-refresh modifications whenever file adjustments occur:
    ```bash
    sudo systemctl daemon-reload
    ```

---

## 📁 Repository & File Structure

Below is the required directory tree layout for your automation assets. Scripts are kept in user or system-level binary paths, while automation profiles reside under `systemd` or user profile paths.

```text
.
├── README.md                           # Main documentation file
├── scripts/                            # Task 1: User-level Cron Script Repository
│   ├── daily_backup.sh                 # Daily incremental data archival script
│   ├── log_monitor.sh                  # Threshold logic health scanning script
│   └── system_info.sh                  # Periodic hardware tracking metrics script
└── systemd/                            # Task 2: System-wide systemd Timers Configuration
    ├── scripts/
    │   ├── temp-cleanup.sh             # Dynamic storage scrubbing binary script
    │   └── system-status.sh            # Root-level metrics collection script
    └── units/
        ├── system-status.service       # Execution engine unit declaration file
        ├── system-status.timer         # Calendar execution schedule entry block
        ├── temp-cleanup.service        # Automation garbage cleanup mapping block
        └── temp-cleanup.timer          # Advanced system configuration timer card
```
