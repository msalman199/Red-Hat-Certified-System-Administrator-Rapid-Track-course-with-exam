# Working with vim and nano Text Editors

A comprehensive lab guide designed to teach core Linux text-processing editors. You will learn to navigate, configure, and manipulate files using both the beginner-friendly `nano` editor and the powerful, modal `vim` ecosystem.

---

## 🎯 Objectives
By the end of this lab, you will be able to:
* **Navigate** and edit files using `vim`'s basic command layouts and modal structures.
* **Create**, modify, and save configuration assets safely using the `nano` editor.
* **Differentiate** between the interface profiles of `vim` and `nano`.
* **Perform** standard text manipulations (cut, copy, paste, undo, find, replace) across both platforms.
* **Commit** changes securely and exit both interfaces without data corruption.
* **Determine** which terminal text editor best fits a given engineering scenario.

---

## 🛠️ Prerequisites & Setup

### Prerequisites
Before starting, ensure you have:
* Basic mastery of the Linux Command Line Interface (CLI).
* Familiarity with navigation structures (`ls`, `cd`, `pwd`) and safe directory actions (`mkdir`).
* Conceptual awareness of Linux user profiles and standard file access controls.

### Lab Environment Setup
This lab is optimized for **Al Nafi Cloud Machines** (CentOS/RHEL-based). The platform delivers fully deployed, native instances of both text utilities alongside target sandbox directories.

---

## 🚀 Lab Tasks

### Task 1: Getting Started with Text Editors

#### 1.1 Understanding Text Editors
Text editors are essential utilities for system administration, infrastructure scripting, and automation maintenance.
* **`nano`**: A direct, linear, modeless text editor that surfaces shortcut keys on screen—perfect for simple adjustments.
* **`vim`**: A high-efficiency, modal text platform that relies on keyboard-driven navigation modes for complex data manipulations.

#### 1.2 Create Practice Directory
Initialize your local isolated sandbox repository:
```bash
mkdir ~/text-editor-lab
cd ~/text-editor-lab
pwd
```

#### 1.3 Check Available Editors
Confirm the path placement and environment versioning profiles:
```bash
which vim
which nano
vim --version | head -1
nano --version | head -1
```

---

### Task 2: Working with nano Editor

#### 2.1 Understanding nano Interface
When initializing files with `nano`, the lower dashboard surfaces key shortcuts explicitly. The caret symbol (`^`) denotes your local **Ctrl** modifier key.

#### 2.2 Create Your First File with nano
```bash
nano server-config.txt
```

#### 2.3 Basic nano Operations
Insert the following configuration lines into the editor:
```ini
# Server Configuration File
# Created on: [Today's Date]

server_name=web-server-01
port=8080
max_connections=100
timeout=30
debug_mode=false

# Database Settings
db_host=localhost
db_port=3306
db_name=webapp
```

#### 2.4 nano Navigation and Editing Shortcuts
* **`Ctrl + A`**: Snap cursor directly to the beginning of the line.
* **`Ctrl + E`**: Snap cursor directly to the end of the line.
* **`Ctrl + Y`**: Scroll page layout up.
* **`Ctrl + V`**: Scroll page layout down.
* **`Ctrl + K`**: Cut the current targeted line into buffer memory.
* **`Ctrl + U`**: Paste line content out from buffer memory.

**Practice Exercise**:
1. Position your cursor over the `port=8080` parameter line.
2. Trigger **`Ctrl + K`** to cut the configuration line out.
3. Advance your cursor immediately below the `server_name=web-server-01` statement.
4. Execute **`Ctrl + U`** to reinsert the statement cleanly.

#### 2.5 Save and Exit nano
* Commit buffer contents: Press **`Ctrl + O`** (WriteOut), then hit **`Enter`** to accept the target name.
* Exit the utility: Press **`Ctrl + X`**.

Verify deployment metrics outside the utility loop:
```bash
ls -la server-config.txt
cat server-config.txt
```

#### 2.6 Reopen and Modify File
```bash
nano server-config.txt
```
Append these explicit entries down at the lowest boundary section:
```ini
# Security Settings
ssl_enabled=true
ssl_port=443
encryption=AES256
```
Save your work (**`Ctrl + O`** -> **`Enter`**) and clear the window instance (**`Ctrl + X`**).

---

### Task 3: Working with vim Editor

#### 3.1 Understanding vim Modes
`vim` separates input mechanics from directory-wide manipulation controls using independent operational modes:
* **Normal Mode (Default)**: Handles text navigation, deletions, line copy actions, and system macro triggers.
* **Insert Mode**: Handles raw text injections.
* **Command Mode**: Handles file management, string searches, and software environment parameter switches.

#### 3.2 Create Your First File with vim
```bash
vim network-settings.conf
```
*Note: You launch explicitly into Normal Mode. Helpful interface indicators do not occupy real estate.*

#### 3.3 Enter Insert Mode and Add Content
Press **`i`** to drop into Insert Mode (look for the green `-- INSERT --` status line down at the bottom). Type in the following environment blocks:

```ini
# Network Configuration
# System: RHEL/CentOS

interface=eth0
ip_address=192.168.1.100
subnet_mask=255.255.255.0
gateway=192.168.1.1
dns_primary=8.8.8.8
dns_secondary=8.8.4.4

# Network Services
ssh_enabled=yes
firewall_enabled=yes
```

#### 3.4 Return to Normal Mode
Press the **`Esc`** key. The bottom mode banner will clear, confirming you are back in Normal Mode.

#### 3.5 Basic vim Navigation in Normal Mode
Avoid relying on legacy directional arrow inputs. Practice tracking with standard row layout binds:
* **`h`**: Move left | **`l`**: Move right | **`k`**: Move up | **`j`**: Move down
* **`w`**: Advance rightward word-by-word.
* **`b`**: Retract leftward word-by-word.
* **`0`**: Snap to the absolute line start.
* **`$`**: Snap to the absolute line terminus.
* **`gg`**: Teleport to the first line of the document file.
* **`G`**: Teleport to the last line of the document file.

#### 3.6 Basic vim Editing Commands
Ensure you are operating out of Normal Mode when calling these execution targets:
* **`x`**: Delete the single specific character resting beneath your cursor.
* **`dd`**: Wipe the targeted line completely out into immediate scratch space.
* **`yy`**: Copy (yank) the entire line context straight into processing storage.
* **`p`**: Append storage buffer elements downstream below your cursor track.
* **`u`**: Roll back the last action sequence step (Undo).
* **`Ctrl + r`**: Push forward past an accidental rollback action (Redo).

**Practice Exercise**:
1. Position yourself directly over the `dns_primary=8.8.8.8` parameter block.
2. Punch **`yy`** to yank that configuration data block cleanly.
3. Hit **`p`** to drop the copy block layout safely down to the next row.
4. Hit **`i`** to re-engage Insert Mode over your new duplicate segment line.
5. Modify the line text properties to read `dns_tertiary=1.1.1.1`.
6. Drop out of writing privileges immediately by punching **`Esc`**.

#### 3.7 Save and Exit vim
Transition from Normal Mode into Command Mode by holding down the **`:`** (colon) key sequence:
* **`:w`**: Write changes down safely to active local storage.
* **`:q`**: Exit clean file states.
* **`:wq`**: Commit operational modifications sequentially and exit the software loop.
* **`:q!`**: Kill the running system window completely, discarding any unsaved work.

Commit your modifications to finish out the section tasks:
Type **`:wq`** and press **`Enter`**.

#### 3.8 Verify and Reopen File
```bash
ls -la network-settings.conf
cat network-settings.conf
vim network-settings.conf
```

#### 3.9 Advanced vim Operations

##### Search and Navigation Tracking
From Normal Mode, punch **`/`** followed immediately by the term you need to isolate:
* Example: Type `/ssh` to find the text string "ssh".
* Press **`n`** to advance to subsequent down-stream query hits.
* Press **`N`** to search in reverse, checking upstream configurations.

##### Line Numbers
* To display structural indexing parameters, input: **`:set number`**
* To turn off the line numbers, input: **`:set nonumber`**

##### Jump directly to explicit line rows
* Jump instantly to line row 5 by keying in: **`:5`**

Practice these configuration combinations, then write out via **`:wq`**.

---

### Task 4: Comparing vim and nano

#### 4.1 Create Comparison Files
Build matching operational references inside both tool workflows:

```bash
# Using nano
nano comparison-nano.txt
```
```text
Editor: nano
Ease of use: Beginner-friendly
Learning curve: Gentle
Best for: Quick edits, beginners
Commands visible: Yes
```
*Commit and close out via **`Ctrl + O`** -> **`Enter`** -> **`Ctrl + X`**.*

```bash
# Using vim
vim comparison-vim.txt
```
*Punch **`i`** to update content strings:*
```text
Editor: vim
Ease of use: Advanced users
Learning curve: Steep
Best for: Complex editing, programming
Commands visible: No
```
*Drop parameters through **`Esc`**, then apply **`:wq`** to finish file write actions.*

#### 4.2 View Both Files
```bash
cat comparison-nano.txt
echo "---"
cat comparison-vim.txt
```

---

### Task 5: Practical Scenarios

#### 5.1 Quick Configuration Edit (nano)
For single-line fixes, `nano` provides rapid deployment with minimal operational friction:
```bash
nano /tmp/httpd.conf
```
Append the standard test block entries listed here:
```apache
# Apache HTTP Server Configuration
ServerRoot "/etc/httpd"
Listen 80
ServerName localhost:80
DocumentRoot "/var/www/html"

# Security Settings
ServerTokens Prod
ServerSignature Off

# Performance Settings
MaxRequestWorkers 150
ThreadsPerChild 25
```
Save your work and exit cleanly.

#### 5.2 Complex File Editing (vim)
When managing multi-line structures, changing matching words, or jumping directly to explicit line indices, `vim` provides superior precision:
```bash
vim /tmp/complex-config.conf
```
*Punch **`i`** and deploy this service schema initialization payload:*
```ini
# Multi-service Configuration
[database]
host=db-server-01
port=5432
username=admin
password=temp123

[webserver]
host=web-server-01
port=80
ssl_port=443
document_root=/var/www

[cache]
host=cache-server-01
port=6379
memory_limit=512M
```
1. Exit writing privileges via **`Esc`**.
2. Activate contextual indexing parameters using **`:set number`**.
3. Teleport directly down onto line row five by typing **`:5`**.
4. Modify the password using the `cw` (change word) shortcut. Move your cursor to the front of `temp123`, type **`cw`**, then enter `secure_password_2026`.
5. Press **`Esc`** to lock the update down.
6. Commit changes to disk via **`:w`**.

#### 5.3 Search and Replace in vim
To rename multiple values across the entire file instantly, execute the global substitution command from Normal Mode:
```text
:%s/server/node/g
```
Press **`Enter`** to process the changes, then type **`:wq`** to save and exit.

---

## 🛡️ Best Practices
* **Avoid Arrow Key Habits in Vim:** Use the `h`, `j`, `k`, `l` key configuration to keep your hands on the home row and increase editing efficiency.
* **Always Verify Hidden Swap Tracks:** If `vim` crashes unexpectedly, don't just force a delete on the warning page. Check for hidden `.swp` backup items to save your missing terminal work.
* **Protect system config parameters:** When updating critical infrastructure directories under `/etc/`, write out your tests using temporary storage domains first before overwriting live configurations.
