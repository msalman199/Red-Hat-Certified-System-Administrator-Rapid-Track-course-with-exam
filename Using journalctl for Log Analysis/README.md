# Using journalctl for Log Analysis

A comprehensive, step-by-step hands-on lab manual for understanding, filtering, and persisting the `systemd` journal. This guide covers deep log analysis using `journalctl`, system troubleshooting techniques, and custom retention tuning.

---

## 🎯 Objectives
By the end of this lab, you will be able to:
* Understand the `systemd` journal architecture and its operational advantages.
* Use `journalctl` to view, scroll, and navigate system logs effectively.
* Filter logs precisely by time bounds, priority flags, and service units.
* Provision persistent log infrastructure for long-term log retention.
* Combine filtering flags to accelerate infrastructure troubleshooting.

---

## 🧰 Tools Reference Guide
The following core binaries and command-line utilities are utilized to complete this lab:


| Utility Name | Primary Purpose in this Lab |
| :--- | :--- |
| `journalctl` | Queries and parses the systemd journal binary logs. |
| `systemctl` | Manages, restarts, and audits the `systemd-journald` daemon service. |
| `logger` | Sends custom shell strings and artificial error flags directly into the log stream. |
| `grep` | Filters outputs for exact string or regex matches (e.g., case-insensitive strings). |
| `ps` | Locates running application Process IDs (PIDs) to target with journal filters. |
| `head` / `cat` | Truncates manual help menus and reads raw file configurations. |

---

## 💻 Lab Environment & Prerequisites

### Prerequisites
* Basic familiarity with the Linux Command Line Interface (CLI).
* Familiarity with `systemd` background service configurations and units.
* Structural knowledge of standard Unix syslog levels (0-7).

### Environment Specs
This lab is designed to run natively on **CentOS / RHEL 8 or 9** platforms using systemd logging mechanisms with root or `sudo` level privileges.
* **OS:** Red Hat Enterprise Linux / CentOS Stream
* **Toolchain:** `systemd-journald`, `journalctl`, `logger`
* **Access:** Full root permission workspace (`sudo su -`)

---

## 🚀 Lab Implementation Steps

### Task 1: View Logs with journalctl

#### Subtask 1.1: Understanding the systemd Journal
Access your systemd architecture and view core runtime metadata options:

```bash
# Elevate current shell session to full root authority
sudo su -

# Review the top 20 lines of the utility configuration manual
journalctl --help | head -20

# Stream standard system journal contents (Press 'q' to exit the view pager)
journalctl
```

#### Subtask 1.2: Basic Log Viewing Commands
Control layout density and view limits inside the active log streams:

```bash
# View the trailing 10 lines of system logs
journalctl -n

# Limit explicit trailing line counts to 20 entries
journalctl -n 20

# Stream arriving system logs actively in real-time (Press Ctrl+C to terminate)
journalctl -f

# View journal events in reverse chronological sorting (Newest lines first)
journalctl -r

# Dump raw entries directly onto stdout bypass text pagination software
journalctl --no-pager -n 5
```

#### Subtask 1.3: Viewing Logs with Different Output Formats
Alter presentation styles to make your data parseable for external processors or analytics software:

```bash
# Format raw system log fields into structured JSON datasets
journalctl -o json -n 3

# Expand lines to display hidden internal systemd metadata tags
journalctl -o verbose -n 2

# Output text via standard human-readable short logging formats
journalctl -o short -n 5
```

---

### Task 2: Filter Logs Based on Time, Priority, and Unit

#### Subtask 2.1: Time-Based Filtering
Isolate incidents by setting explicit chronological query boundaries:

```bash
# Extract log data generated since midnight of the current calendar day
journalctl --since today

# Isolate exactly what events occurred during the previous calendar day
journalctl --since yesterday --until today

# Fetch entries emitted during the immediate preceding hour
journalctl --since "1 hour ago"

# Enforce explicit timestamp boundaries for surgical forensic isolation
journalctl --since "2024-01-01 00:00:00" --until "2024-01-01 23:59:59"

# Fetch entries captured over the last thirty minutes
journalctl --since "30 minutes ago"
```

#### Subtask 2.2: Priority-Based Filtering
Filter by Syslog severity classifications: `0: emerg`, `1: alert`, `2: crit`, `3: err`, `4: warning`, `5: notice`, `6: info`, `7: debug`.

```bash
# Filter for error statements and higher priority events
journalctl -p err

# Isolate warnings and everything more severe
journalctl -p warning

# Surface critical errors requiring immediate operator attention
journalctl -p crit

# Generate artificial testing signatures to populate priority logs
logger -p user.err "This is a test error message"
logger -p user.warning "This is a test warning message"
logger -p user.info "This is a test info message"

# Filter current journal content to locate your generated test signatures
journalctl -p info --since "1 minute ago" | grep "test"
```

#### Subtask 2.3: Unit-Based Filtering
Target specific background microservices, core components, or operating processes:

```bash
# Sample up to ten systemd service units that have compiled messages
journalctl -F _SYSTEMD_UNIT | head -10

# Audit log streams specifically owned by the OpenSSH Server daemon
journalctl -u sshd

# Review records generated by the network interface daemon
journalctl -u NetworkManager

# Aggregate multiple isolated server infrastructure components concurrently
journalctl -u sshd -u NetworkManager --since today

# Extract logs coming strictly out of the system kernel rings
journalctl -k

# Locate process identifiers via ps, then track that specific execution target
ps aux | grep systemd | head -1

# Filter the log stream by the targeted numeric Process ID (PID)
journalctl _PID=1 -n 5
```

#### Subtask 2.4: Combining Filters
Chain multi-variable selectors together to locate complex target states instantly:

```bash
# Query error-level entries logged by the SSH service during the current day
journalctl -u sshd -p err --since today

# Isolate all structural warning/error entries generated within the last 2 hours
journalctl -p warning --since "2 hours ago"

# Scan NetworkManager info-level logs active during the previous calendar day
journalctl -u NetworkManager -p info --since yesterday --until today
```

---

### Task 3: Set Up Persistent Log Storage

#### Subtask 3.1: Understanding Journal Storage
Inspect whether the current system retains logs across host restarts:

```bash
# Read current storage footprint sizes on disk storage assets
journalctl --disk-usage

# Examine active system-wide logging configuration blueprints
cat /etc/systemd/journald.conf

# Check for existence of the default persistent storage root path
ls -la /var/log/journal/
```
*Note: If the directory above throws missing or empty faults, logs sit entirely in volatile memory (`/run/log/journal`) and vanish on host reboots.*

#### Subtask 3.2: Enable Persistent Storage
Reconfigure system log settings to enforce disk storage parameters:

```bash
# Force provision the expected storage target directory path
mkdir -p /var/log/journal

# Enforce secure system access ownership settings and sticky bit bitmasks
chown root:systemd-journal /var/log/journal
chmod 2755 /var/log/journal

# Keep a safe operational backup copies of the standard configurations
cp /etc/systemd/journald.conf /etc/systemd/journald.conf.backup

# Overwrite active journal configuration files with persistent runtime metrics
cat > /etc/systemd/journald.conf << 'EOF'
[Journal]
Storage=persistent
Compress=yes
SyncIntervalSec=5m
RateLimitInterval=30s
RateLimitBurst=1000
SystemMaxUse=500M
SystemKeepFree=1G
SystemMaxFileSize=50M
MaxRetentionSec=1month
MaxFileSec=1week
ForwardToSyslog=no
ForwardToKMsg=no
ForwardToConsole=no
ForwardToWall=yes
EOF

# Restart the logging service instance to commit persistent behaviors
systemctl restart systemd-journald

# Check service operational health to verify parameters
systemctl status systemd-journald
```

#### Subtask 3.3: Verify Persistent Storage Configuration
Confirm the backend logging service correctly maps data streams into disk structures:

```bash
# Check the storage mount directory state
ls -la /var/log/journal/

# Enumerate machine-id subdirectories holding log files
ls -la /var/log/journal/*/

# Check file allocation footprints
journalctl --disk-usage

# Emit a loop test sequence to populate logging engines
for i in {1..10}; do
    logger "Test persistent log entry \$i"
done

# Confirm tracking systems parse new additions within the timeframe window
journalctl --since "1 minute ago" | grep "Test persistent"
```

#### Subtask 3.4: Managing Journal Storage
Perform filesystem housekeeping operations to control storage sizes:

```bash
# Request verification audit loops over existing binary journals
journalctl --disk-usage
journalctl --verify

# Flush logging segments aging past a 2-day expiration boundary
journalctl --vacuum-time=2d

# Compress active file storage footprints down below a 100 Megabyte limit
journalctl --vacuum-size=100M

# Prune structures down to contain a maximum count of 50 active log files
journalctl --vacuum-files=50

# Request running operating metrics directly from systemd controls
systemctl show systemd-journald | grep -E "(Storage|MaxUse|KeepFree)"
```

---

### Task 4: Advanced Filtering and Analysis

#### Subtask 4.1: Advanced Query Techniques
Extract values using internal search engines and system boot parameters:

```bash
# Pipeline raw output data through case-insensitive text scanning utilities
journalctl | grep -i "error"

# Query string values natively using optimization flags
journalctl -g "failed"

# Track structural system entries mapped to User ID 0 (root user context)
journalctl _UID=0 -n 10

# Catalog recorded host startup boots preserved inside the infrastructure
journalctl --list-boots

# View logs for the current boot session
journalctl -b 0

# Review logs captured during the previous system boot session
journalctl -b -1

# Export logs to a structured file for external security information parsing
journalctl --since today --output=json > /tmp/today_logs.json
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
By implementing this lab guide, you have transitioned from basic text-based terminal logs to managing a high-performance, structured logging engine via `journalctl`.

Key concepts mastered in this exercise include:
* **Centralized Binary Logging:** Transitioning past antiquated plain-text syslog paradigms to query indexed binary logs.
* **Surgical Filtering:** Chaining boundaries (`-u`, `-p`, `--since`) to discover error states within seconds instead of scrolling files.
* **Log Lifecycle Management:** Enforcing disk limits, sync times, and data encryption options inside `journald.conf` to safely prevent storage exhaustion.
* **Forensic Boot Analysis:** Tracking system reboots and cross-examining past runtime conditions via boot indicators (`-b`).

These core analytical capabilities are necessary for ensuring application uptime, monitoring security boundaries, and debugging distributed infrastructure.
