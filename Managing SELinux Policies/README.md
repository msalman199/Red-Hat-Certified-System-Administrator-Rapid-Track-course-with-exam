# 🛡️ Managing SELinux Policies 

This repository contains the official course materials, dynamic command steps, and automated scripts for the Managing SELinux (Security-Enhanced Linux) Policies Lab. Students will master Mandatory Access Control (MAC), context modifications, boolean toggles, and systematic log auditing workflows.

---

## 🎯 Objectives

By the end of this lab, you will be able to:
* **Understand fundamentals** of SELinux architectures and its security enforcement models.
* **Check and interpret** structural system containment modes using `sestatus`.
* **Modify runtime policies** safely using the `semanage` command-line ecosystem.
* **Troubleshoot denials** via audit logs (`ausearch`, `sealert`, `audit2allow`).
* **Configure persistent contexts** and Boolean settings for modern enterprise applications.
* **Apply security hardening** best practices on production servers.

---

## 📋 Prerequisites

Before starting this lab, you should have:
* Basic proficiency with the Linux command line interface (CLI).
* Familiarity with standard discretionary file permissions and ownership concepts.
* Core system administration foundations and experience reviewing log output.
* Access to a Red Hat-based Linux distribution (RHEL 9, CentOS Stream 9, or Fedora).

---

## 💻 Lab Environment Setup

### Ready-to-Use Cloud Machines
Al Nafi provides pre-configured Linux-based cloud machines for this lab. Simply click **Start Lab** to launch your environment without building local virtual machines.

### Environment Components:
* **OS:** Red Hat Enterprise Linux 9 or CentOS Stream 9 system.
* **State:** SELinux is actively enabled and configured in `Enforcing` mode.
* **Tools:** Management packages (`policycoreutils`, `semanage`) are ready out-of-the-box.
* **Targets:** Pre-installed sample applications and services for exploitation testing.

---

## 🛠️ Lab Tasks & Instructions

### Task 1: Check SELinux Status with sestatus

#### Subtask 1.1: Understanding SELinux Basics
* **Step 1: Verify root environment privileges**
  ```bash
  sudo whoami
  ```
* **Step 2: Display comprehensive SELinux status parameters**
  ```bash
  sestatus
  ```
  *Expected Output Analysis:*
  ```text
  SELinux status:                 enabled
  SELinuxfs mount:                /sys/fs/selinux
  SELinux root directory:         /etc/selinux
  Loaded policy name:             targeted
  Current mode:                   enforcing
  Mode from config file:          enforcing
  Policy MLS status:              enabled
  Policy deny_unknown status:     allowed
  Memory protection checking:     actual (secure)
  Max kernel policy version:      33
  ```

#### Subtask 1.2: Understanding SELinux Modes
* **Step 3: Check current active operational enforcement mode**
  ```bash
  getenforce
  ```
* **Step 4: View the persistent configuration mapping file**
  ```bash
  cat /etc/selinux/config
  ```
  *Key Concept Matrix:*
  * **Enforcing:** Policy is actively enforced. Denials are blocked and logged.
  * **Permissive:** Policy rules are checked but not blocked. Denials are only logged.
  * **Disabled:** SELinux kernel hooks are turned off completely.

#### Subtask 1.3: Examining SELinux Contexts
* **Step 5: View target folder file context strings**
  ```bash
  ls -Z ~/
  ```
* **Step 6: Trace process mapping labels**
  ```bash
  ps -eZ | head -10
  ```
* **Step 7: Identify user token identity context binding**
  ```bash
  id -Z
  ```

---

### Task 2: Modify SELinux Policies with semanage

#### Subtask 2.1: Installing SELinux Management Tools
* **Step 8: Provision required utility suites**
  ```bash
  sudo dnf install -y policycoreutils-python-utils setools-console
  ```
* **Step 9: Confirm binary locations and functional syntax help**
  ```bash
  which semanage
  semanage --help
  ```

#### Subtask 2.2: Managing SELinux File Contexts
* **Step 10: Build a localized testing directories stack**
  ```bash
  mkdir -p /opt/testapp
  sudo touch /opt/testapp/config.conf
  ```
* **Step 11: Read default context inheritance variables**
  ```bash
  ls -Z /opt/testapp/
  ```
* **Step 12: Inject a custom web configuration context rule persistently**
  ```bash
  sudo semanage fcontext -a -t httpd_config_t "/opt/testapp/config.conf"
  ```
* **Step 13: Refresh kernel properties definition tracking mapping updates**
  ```bash
  sudo restorecon -v /opt/testapp/config.conf
  ```
* **Step 14: Confirm persistent context manipulation changes**
  ```bash
  ls -Z /opt/testapp/config.conf
  ```

#### Subtask 2.3: Managing SELinux Port Contexts
* **Step 15: Isolate registered networks application bindings**
  ```bash
  semanage port -l | grep http
  ```
* **Step 16: Label alternative web ports into target access domains**
  ```bash
  sudo semanage port -a -t http_port_t -p tcp 8080
  ```
* **Step 17: Review assigned port bindings validation maps**
  ```bash
  semanage port -l | grep 8080
  ```

#### Subtask 2.4: Managing SELinux Booleans
* **Step 18: Sample existing functional engine toggles**
  ```bash
  getsebool -a | head -10
  ```
* **Step 19: Inspect default network flags routing permissions**
  ```bash
  getsebool httpd_can_network_connect
  ```
* **Step 20: Adjust working system values volatile space state**
  ```bash
  sudo setsebool httpd_can_network_connect on
  ```
* **Step 21: Flush values persistently into system configurations layer store**
  ```bash
  sudo setsebool -P httpd_can_network_connect on
  ```
* **Step 22: Confirm boolean modifications target changes state flag**
  ```bash
  getsebool httpd_can_network_connect
  ```

#### Subtask 2.5: Managing SELinux User Mappings
* **Step 23: Review OS account login domain interactions map**
  ```bash
  semanage login -l
  ```
* **Step 24: Display context classifications groups database listing**
  ```bash
  semanage user -l
  ```

---

### Task 3: Troubleshoot SELinux Denials Using Audit Logs

#### Subtask 3.1: Understanding SELinux Audit Logs
* **Step 25: Install structural diagnostics trace translation server components**
  ```bash
  sudo dnf install -y setroubleshoot-server
  ```
* **Step 26: Assess core security recording sub-engine service health metrics**
  ```bash
  sudo systemctl status auditd
  ```
* **Step 27: Filter access validation check errors tracking events**
  ```bash
  sudo ausearch -m avc -ts recent
  ```

#### Subtask 3.2: Creating and Analyzing SELinux Denials
* **Step 28: Establish alternative application root test paths**
  ```bash
  sudo mkdir -p /home/testuser/website
  sudo echo "<html><body>Test Page</body></html>" | sudo tee /home/testuser/website/index.html
  sudo chown -R apache:apache /home/testuser/website
  ```
* **Step 29: Install Apache HTTP engine baseline packages**
  ```bash
  sudo dnf install -y httpd
  sudo systemctl start httpd
  sudo systemctl enable httpd
  ```
* **Step 30: Direct the web services config towards custom storage targets**
  ```bash
  sudo tee /etc/httpd/conf.d/testsite.conf << EOF
  <VirtualHost *:80>
      DocumentRoot /home/testuser/website
      <Directory "/home/testuser/website">
          AllowOverride None
          Require all granted
      </Directory>
  </VirtualHost>
  EOF
  ```
* **Step 31: Cycle process state loops to encounter contextual policy violations**
  ```bash
  sudo systemctl restart httpd
  ```
* **Step 32: Trigger traffic endpoints execution loops locally**
  ```bash
  curl http://localhost/
  ```

#### Subtask 3.3: Analyzing SELinux Denials
* **Step 33: Fetch generated AVC alerts from recent telemetry streams**
  ```bash
  sudo ausearch -m avc -ts recent
  ```
* **Step 34: Execute deep auditing logs diagnosis tool analyzer engines**
  ```bash
  sudo sealert -a /var/log/audit/audit.log
  ```
* **Step 35: Query text strings trace records directly from target stores**
  ```bash
  sudo grep "denied" /var/log/audit/audit.log | tail -5
  ```

#### Subtask 3.4: Resolving SELinux Denials
* **Step 36: Evaluate the faulty folder mapping target paths**
  ```bash
  ls -Z /home/testuser/website/
  ```
* **Step 37: Enforce explicit directory file type declarations policies recursively**
  ```bash
  sudo semanage fcontext -a -t httpd_sys_content_t "/home/testuser/website(/.*)?"
  sudo restorecon -R -v /home/testuser/website/
  ```
* **Step 38: *(Alternative Approach)* Reference baseline directory structures metadata attributes manually**
  ```bash
  sudo chcon --reference=/var/www/html /home/testuser/website/index.html
  ```
* **Step 39: Validate application deployment target properties updates state**
  ```bash
  ls -Z /home/testuser/website/
  ```
* **Step 40: Re-run local traffic endpoint diagnostic validation processes**
  ```bash
  curl http://localhost/
  ```

#### Subtask 3.5: Using SELinux Troubleshooting Tools
* **Step 41: Grab explicit auxiliary management components wrappers packages**
  ```bash
  sudo dnf install -y policycoreutils-gui
  ```
* **Step 42: Synthesize functional remediation structural baseline rules packages**
  ```bash
  sudo grep httpd /var/log/audit/audit.log | audit2allow -M myhttpd
  ```
* **Step 43: Audit autogenerated enforcement policy expressions structures source files**
  ```bash
  cat myhttpd.te
  ```
* **Step 44: Incorporate dynamic compiled packages structures tracking arrays cleanly**
  ```bash
  sudo semodule -i myhttpd.pp
  ```

---

### Advanced SELinux Management

#### Subtask 3.6: SELinux Policy Modules
* **Step 45: Discover system modules running tracking frameworks stack**
  ```bash
  semodule -l | head -10
  ```
* **Step 46: Verify state configurations updates matrix properties logs**
  ```bash
  semodule -l | grep httpd
  ```
* **Step 47: Check current configuration properties compilation specifications**
  ```bash
  sestatus | grep "policy version"
  ```

#### Subtask 3.7: SELinux Monitoring and Maintenance
* **Step 48: Establish system automated reporting diagnostic telemetry configurations scripts**
  ```bash
  sudo tee /usr/local/bin/selinux-monitor.sh << 'EOF'
  #!/bin/bash
  echo "=== SELinux Status ==="
  sestatus

  echo -e "\n=== Recent SELinux Denials ==="
  ausearch -m avc -ts today 2>/dev/null | tail -10

  echo -e "\n=== SELinux Boolean Status ==="
  getsebool -a | grep "on$" | wc -l
  echo "Total booleans enabled"

  echo -e "\n=== Custom File Contexts ==="
  semanage fcontext -l -C
  EOF

  sudo chmod +x /usr/local/bin/selinux-monitor.sh
  ```
* **Step 49: Manually test execution steps on tracking pipeline tooling scripts**
  ```bash
  sudo /usr/local/bin/selinux-monitor.sh
  ```
* **Step 50: Inject scheduled tasks entry definitions parameters pipelines records**
  ```bash
  echo "0 */6 * * * root /usr/local/bin/selinux-monitor.sh >> /var/log/selinux-monitor.log 2>&1" | sudo tee -a /etc/crontab
  ```

---

### ⚠️ Troubleshooting Common Issues

* **Issue 1: Context reset after server reboot**
  * *Cause:* Using `chcon` temporary flags instead of using persistent `semanage fcontext` rules database changes.
  * *Solution:* Apply directory tracking updates paths configuration via `semanage fcontext`, then execute file tracking re-indexing rules explicitly with `restorecon`.

* **Issue 2: `semanage` command completely missing or throwing terminal execution errors**
  * *Cause:* Necessary helper utility core packages missed initialization hooks.
  * *Solution:* Execute systemic system updates explicitly targeting `sudo dnf install -y policycoreutils-python-utils`.

---

## 📂 File Structure

Below is the directory architecture for this repository:

```text
selinux-lab/
├── docs/
│   ├── Task1_Sestatus_Basics.md     # Detailed logs of contexts, states, and user maps
│   ├── Task2_Semanage_Policies.md   # Record of port binding rules and booleans modified
│   └── Task3_Audit_Trouble.md       # Full breakdown of audit records and AVC solutions
├── scripts/
│   ├── web_scenario_setup.sh        # Automates Apache setup on non-standard directory
│   └── fix_denials.sh               # Shell script executing semanage rules for tasks
├── tools/
│   ├── selinux-monitor.sh           # Main telemetry metrics verification collection tool
│   └── clean_audit_cache.sh         # Helper tool to flush testing log profiles
├── LICENSE                          # Lab distribution license file
└── README.md                        # Primary lab documentation guide
```

---

## 🏁 Conclusion

This laboratory exercise builds a holistic, operational perspective on protecting files and network applications using SELinux engine configurations. Developing systematic workflows for context correction, system boolean toggling, and AVC denial remediation equips system architects to manage high-security parameters inside modern cloud computing runtime frames.
