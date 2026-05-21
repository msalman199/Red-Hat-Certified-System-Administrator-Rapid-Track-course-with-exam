# Introduction to Containers with Podman

A comprehensive, step-by-step hands-on lab manual for installing, configuring, and testing `podman` to orchestrate daemonless, rootless containers on Linux environments. This guide covers lifecycle workflows, image building, networks, and persistent storage.

---

## 🎯 Objectives
By the end of this lab, you will be able to:
* Understand containerization principles and Podman’s daemonless architecture.
* Install, configure, and audit Podman on enterprise Linux platforms.
* Manage images by searching registries, pulling layers, and mapping custom tags.
* Master the container lifecycle (initializing, stopping, starting, and pruning).
* Build custom images using declarative local `Containerfile` configurations.
* Implement container persistence using named software-defined volumes.
* Configure isolated user-defined networks for multi-tier container workflows.

---

## 🧰 Tools & Commands Matrix
The following table outlines the foundational system binaries and utilities used throughout this lab:


| Utility Name | Primary Purpose in this Lab | Common Use Case Example |
| :--- | :--- | :--- |
| `podman` | Core containerization engine to manage images, volumes, and networks. | `podman run ...` |
| `dnf` | System package manager to pull the Podman ecosystem from repositories. | `sudo dnf install podman` |
| `curl` | Command-line network diagnostic tool used to check local ports. | `curl http://localhost:8080` |
| `ps` | System process viewer to inspect isolated tasks inside containers. | `ps aux` |
| `cat` / `echo` | File system streams used to print variables and write build schemas. | `cat > Containerfile` |

---

## 💻 Lab Environment & Prerequisites

### Prerequisites
* Basic knowledge of Linux command line operations.
* Understanding of file system structures, directories, and file permissions.
* Familiarity with package managers (`dnf`) and basic TCP/IP networking (ports, IPs).
* Text editing skills using terminal-based utilities (`nano`, `vim`, or `cat`).

### Environment Specs
This lab is optimized for systemd-managed enterprise environments with root or `sudo` access blocks:
* **OS:** Red Hat Enterprise Linux / CentOS Stream (8 or 9)
* **Connectivity:** Unrestricted outbound WAN internet access for container registry lookup.

---

## 🚀 Lab Implementation Steps

### Task 1: Install Podman and Create Containers

#### Subtask 1.1: Install Podman
Update infrastructure endpoints and fetch the container engine binaries:

```bash
# Step 1: Synchronize and pull latest system package security upgrades
sudo dnf update -y

# Step 2: Install the Podman application binary packages
sudo dnf install -y podman

# Step 3: Verify the runtime core version string matches installation metrics
podman --version

# Step 4: Gather extensive structural status and configuration data
podman info
```

---

## 📁 Repository & Application File Structure
Now that Podman is installed, you will build and organize your workspace files. Implement the following directory layout inside your repository to support the custom application and build steps in the next tasks:

```text
📁 podman-container-lab/
├── 📄 README.md                 # Main lab documentation and workflow instructions
├── 📁 custom-web-app/           # Staging directory for Task 2.3 custom application
│   ├── 📄 Containerfile         # Declarative build schema for custom image creation
│   └── 📄 index.html            # Custom HTML welcome landing page artifact
└── 📁 scripts/                  # Automation directory
    └── ⚙️ cleanup.sh            # Custom environmental resource cleanup script
```

---

### Task 2: Manage Container Images with Podman

#### Subtask 2.1: Working with Container Images
Scan the engine's local storage layers and evaluate upstream remote image endpoints:

```bash
# Step 1: List all existing locally downloaded registry image layers
podman images

# Step 2: Scan public upstream hub registries for specific engine names
podman search nginx

# Step 3: Fetch an explicit version parameter string tag over minimal Alpine layers
podman pull docker.io/library/nginx:1.21-alpine

# Step 4: Dump structural JSON layer parameters and target execution metadata
podman inspect httpd:latest
```

#### Subtask 2.2: Image Management Operations
Alter local tag maps to structure staging revisions and prune intermediate build caches:

```bash
# Step 1: Generate a custom tracking version tag map over the local HTTPD system
podman tag httpd:latest my-custom-web:v1.0

# Step 2: Filter image layers to confirm your tracking alias applied correctly
podman images | grep my-custom-web

# Step 3: Remove your tracking target tag string from local storage map
podman rmi my-custom-web:v1.0

# Step 4: Safely wipe dangling, untagged images to clear host space constraints
podman image prune
```

#### Subtask 2.3: Creating a Custom Image
Construct your application directory layout based on the file structure shown above, declare parameters in a `Containerfile`, and build custom code:

```bash
# Step 1: Create the custom web app directory as specified in the file structure
mkdir -p ~/custom-web-app
cd ~/custom-web-app

# Step 2: Write out a structural source HTML web application landing page
cat > index.html << 'EOF'
<!DOCTYPE html>
<html>
<head>
    <title>My Custom Web App</title>
</head>
<body>
    <h1>Welcome to My Container Lab</h1>
    <p>This is a custom web application running in Podman!</p>
    <p>Lab 18: Introduction to Containers with Podman</p>
</body>
</html>
EOF

# Step 3: Declare multi-tiered image configuration instructions inside a Containerfile
cat > Containerfile << 'EOF'
FROM httpd:latest
COPY index.html /usr/local/apache2/htdocs/
EXPOSE 80
EOF

# Step 4: Execute the engine builder to compile your layers locally
podman build -t my-web-app:latest .

# Step 5: Start a container using your custom application image
podman run -d --name custom-web -p 8081:80 my-web-app:latest

# Step 6: Query your endpoint web application server via port mapping loops
curl http://localhost:8081
```

---

### Task 3: Run and Manage Container Services

#### Subtask 3.1: Container Lifecycle Management
Review structural logs, stop running environments, and manage overall state changes:

```bash
# Step 1: List all container references across all active and stopped states
podman ps -a

# Step 2: Gracefully shut down a target container server process
podman stop my-web-server

# Step 3: Spin back up a stopped target infrastructure container instance
podman start my-web-server

# Step 4: Force-restart an application container process
podman restart my-web-server

# Step 5: Dump current standard output (stdout) server logs
podman logs my-web-server

# Step 6: Bind terminal to continuously follow arriving system server log lines (Ctrl+C to break)
podman logs -f my-web-server
```

#### Subtask 3.2: Advanced Container Operations
Inject tracking commands into a live environment and execute manual file copy routines:

```bash
# Step 1: Attach an interactive bash subshell process directly inside a running web container
podman exec -it my-web-server /bin/bash

# [INSIDE APP CONTAINER CONTEXT] View configuration setups and exit
ls -la /usr/local/apache2/conf/
cat /usr/local/apache2/htdocs/index.html
exit

# Step 2: Move file properties cleanly across host system boundaries
echo "This file was copied from host" > host-file.txt
podman cp host-file.txt my-web-server:/usr/local/apache2/htdocs/
podman cp my-web-server:/usr/local/apache2/htdocs/index.html ./container-index.html

# Step 3: Extract direct performance telemetry metrics (CPU, Memory, IO thresholds)
podman stats my-web-server
```

#### Subtask 3.3: Working with Container Volumes
Persist internal runtime data outside standard boundary lifecycles using volumes:

```bash
# Step 1: Allocate a persistent software-defined named container storage volume
podman volume create web-data

# Step 2: Enumerate persistent volumes mapped on host structures
podman volume ls

# Step 3: Run an application container instance with the volume bound to the web storage path
podman run -d --name web-with-volume -p 8082:80 -v web-data:/usr/local/apache2/htdocs httpd:latest

# Step 4: Push an HTML update directly into the volume context space
podman exec web-with-volume sh -c 'echo "<h1>Data from Volume</h1>" > /usr/local/apache2/htdocs/volume.html'

# Step 5: Run a network scan verification loop on the target data path
curl http://localhost:8082/volume.html

# Step 6: Identify host mountpoint paths and verify volume parameters
podman volume inspect web-data
```

#### Subtask 3.4: Container Networking
Set up isolated custom database subnets to safely verify internal cross-container name resolutions:

```bash
# Step 1: Provision a distinct isolated user-defined system bridge network overlay
podman network create lab-network

# Step 2: Enumerate network interface schemas managed by Podman
podman network ls

# Step 3: Run target microservices side-by-side inside the custom subnet
podman run -d --name lab-database --network lab-network -e MYSQL_ROOT_PASSWORD=labpassword mysql:8.0
podman run -d --name lab-webapp --network lab-network -p 8083:80 httpd:latest

# Step 4: Inject debugging utilities inside the webapp layer to run diagnostic checks
podman exec lab-webapp apt-get update
podman exec lab-webapp apt-get install -y iputils-ping

# Ping the companion data container directly by name via software DNS resolution
podman exec lab-webapp ping -c 3 lab-database
```

#### Subtask 3.5: Container Cleanup
Wipe running systems, clean volume endpoints, and release unassigned resources:

```bash
# Step 1: Harvest active process identifiers to stop all running instances
podman stop \$(podman ps -q)

# Step 2: Delete specific named workspace containers from memory records
podman rm my-web-server custom-web interactive-centos

# Step 3: Erase a container alongside its associated anonymous storage systems
podman rm -v web-with-volume

# Step 4: Purge unassigned dangling configuration files and stale infrastructure networks
podman container prune -f
podman image prune -a -f
podman volume prune -f
podman network prune -f

# Step 5: Validate total environmental system cleanup configurations
podman ps -a
podman images
podman volume ls
podman network ls
```

---

## 🛠️ Troubleshooting Tips

### Issue 1: Permission denied when running Podman commands
* **Cause:** Your current user account lacks root privilege access blocks, or the rootless user configurations fail subuid/subgid mapping queries.
* **Solution:** Verify that your account is listed inside the `sudoers` file, run the commands using `sudo`, or validate that your rootless user context correctly sets up subordinate user ID mappings in `/etc/subuid` and `/etc/subgid`.

### Issue 2: Error pulling images from registry
* **Cause:** Podman cannot parse short image alias properties, or your upstream corporate networking filters are blocking target domains.
* **Solution:** Provide full qualified image domain registry paths (e.g., use `docker.io/library/httpd:latest` explicitly rather than relying purely on a short `httpd` name string query).

### Issue 3: Port allocation conflicts (e.g., port already in use)
* **Cause:** The network transport layer port on the host system (such as `8080`) is already bound to another active service process.
* **Solution:** Audit running system ports using `ss -tulpn` or `netstat`. Adjust the host port binding configuration within your `podman run` instruction line mapping syntax (e.g., change `-p 8080:80` to `-p 8090:80`).

---

## 🏁 Conclusion
By executing this lab documentation configuration, you have transitioned from standard system structures to provisioning containerized application stacks using `podman`.

Key takeaways from this exercise include:
* **Daemonless Execution:** Running applications cleanly without relying on high-risk, background root-level processes, significantly improving host security profiles.
* **Microservices Segmentation:** Building network bridges (`podman network`) that handle automatic service name lookups between application instances.
* **Data Lifecycle Isolation:** Using persistent storage boundaries (`podman volume`) to ensure database application information persists during system image refactoring or clean rebuild events.
* **Custom Compilation Layers:** Structuring automated deployments via the standard declarative rules defined inside a `Containerfile`.

Adopting light, declarative container setups forms a foundational pillar for establishing modern DevOps pipelines, automated testing, and scalable microservice architectures.
