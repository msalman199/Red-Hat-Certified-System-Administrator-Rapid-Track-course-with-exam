# Managing Storage with NFS and Ceph

A comprehensive, step-by-step hands-on lab manual for configuring, deploying, and integrating both Network-Attached Storage (NFS) and Ceph distributed block storage clusters within a Kubernetes ecosystem.

---

## 🎯 Objectives
By the end of this lab, you will be able to:
* Configure and deploy an NFS (Network File System) server for shared multi-node storage.
* Initialize and set up a multi-node Ceph distributed storage cluster for raw block storage.
* Integrate traditional NFS shares and advanced Ceph block pools with Kubernetes.
* Articulate the design and performance differences between Network-Attached Storage (NAS) and Distributed Block Devices.
* Provision static and dynamic Persistent Volumes (PVs) and Persistent Volume Claims (PVCs) in Kubernetes clusters.
* Troubleshoot everyday cluster, filesystem routing, and volume attachment errors.

---

## 🧰 Tools & Commands Matrix
The following table outlines the foundational system utilities used throughout this lab deployment:


| Utility Name | Primary Purpose in this Lab | Common Use Case Example |
| :--- | :--- | :--- |
| `exportfs` | Manages, reloads, and audits active NFS filesystem exports. | `sudo exportfs -v` |
| `cephadm` | Orchestrates, bootstraps, and adds cluster host nodes for Ceph Pacific. | `sudo cephadm bootstrap ...` |
| `ceph` | General administrative tool to inspect pools, OSD states, and status flags. | `sudo ceph status` |
| `rbd` | Configures RADOS Block Devices, provisioning and mapping disk images. | `sudo rbd create ...` |
| `kubectl` | Orchestrates storage assets (PV, PVC, StorageClasses) inside Kubernetes. | `kubectl apply -f ...` |
| `mount` | Binds remote network shares or block filesystems to a local folder mountpoint. | `sudo mount -t nfs ...` |

---

## 💻 Lab Environment & Prerequisites

### Prerequisites
* Basic understanding of Linux command-line operations and folder hierarchies.
* Familiarity with block storage filesystems (ext4/XFS) and standard permission blocks.
* Basic knowledge of Kubernetes primitives (Pods, Deployments, Persistent Volumes).
* Functional networking skills (IP spaces, routing paths, custom interface configurations).
* Proficiency with console editors like `vi/vim`, `nano`, or `cat` streams.

### Environment Specs
The pre-configured environment layout provided for this workspace contains:
* **Nodes:** 4 virtual machines running CentOS / RHEL 8 with root access.
* **Orchestration Platform:** Kubernetes container engine pre-installed on nodes.
* **Networking:** Clean internal host-to-host private subnet connectivity.

---

## 🚀 Lab Implementation Steps

### Task 1: Configure an NFS Server

#### Subtask 1.1: Install and Configure NFS Server
Establish shared folder directories on your designated storage controller host (**server1**):

```bash
# Step 1: Update existing repository packages and download the NFS daemon
sudo dnf update -y
sudo dnf install -y nfs-utils

# Step 2: Create directory trees to act as shared network nodes
sudo mkdir -p /nfs/shared
sudo mkdir -p /nfs/data

# Bind directory access restrictions to liberal reading states
sudo chmod 755 /nfs/shared
sudo chmod 755 /nfs/data

# Map directory user ownership to 'nobody' for anonymous client mapping safety
sudo chown nobody:nobody /nfs/shared
sudo chown nobody:nobody /nfs/data

# Step 3: Populate file exports using nano configuration sheets
sudo nano /etc/exports
```

Add the following exact configurations to the `/etc/exports` file:
```text
/nfs/shared *(rw,sync,no_subtree_check,no_root_squash)
/nfs/data *(rw,sync,no_subtree_check,no_root_squash)
```

```bash
# Step 4: Start, daemonize, and run local network file systems
sudo systemctl enable rpcbind nfs-server
sudo systemctl start rpcbind nfs-server

# Force export the current rules configuration sheet and verify active properties
sudo exportfs -a
sudo exportfs -v
```

#### Subtask 1.2: Configure NFS Client
Connect to your consumer node configuration host (**server2**) and map the shares:

```bash
# Step 1: Attach client utilities and establish mountpoint folder structures
sudo dnf install -y nfs-utils
sudo mkdir -p /mnt/nfs-shared
sudo mkdir -p /mnt/nfs-data

# Step 2: Execute mount commands (Replace SERVER_IP with server1's actual IP string)
SERVER_IP="192.168.1.10"
sudo mount -t nfs ${SERVER_IP}:/nfs/shared /mnt/nfs-shared
sudo mount -t nfs ${SERVER_IP}:/nfs/data /mnt/nfs-data

# Verify storage sizes and mount paths
df -h | grep nfs

# Step 3: Write out a test string file to ensure write permissions pass cleanly
echo "Hello from NFS client" | sudo tee /mnt/nfs-shared/test.txt

# (Validation) Move back to server1 console to confirm synchronization across interfaces
cat /nfs/shared/test.txt

# Step 4: Edit local file tables to ensure paths re-mount on system restarts
echo "${SERVER_IP}:/nfs/shared /mnt/nfs-shared nfs defaults 0 0" | sudo tee -a /etc/fstab
echo "${SERVER_IP}:/nfs/data /mnt/nfs-data nfs defaults 0 0" | sudo tee -a /etc/fstab

# Test consistency by unmounting filesystems and attempting automatic mounts
sudo umount /mnt/nfs-shared /mnt/nfs-data
sudo mount -a
```

---

### Task 2: Set up Ceph for Distributed Block Storage

#### Subtask 2.1: Install Ceph Components
Prepare three-node baseline infrastructures (**server1**, **server2**, **server3**) and initialize parameters:

```bash
# Run these three lines on all cluster nodes respectively:
sudo dnf install -y centos-release-ceph-pacific
sudo dnf install -y ceph ceph-radosgw
sudo dnf install -y cephadm

# Run bootstrap step ONLY on the primary manager host controller node (server1):
sudo cephadm bootstrap --mon-ip 192.168.1.10
# Note: Save the output credential dashboard password and URL string printed to console!

# Run host addition commands from the primary manager prompt (server1):
sudo ceph orch host add server2 192.168.1.11
sudo ceph orch host add server3 192.168.1.12

# Review active cluster health markers
sudo ceph status
```

#### Subtask 2.2: Configure Ceph Storage
Bind raw disks into the Object Storage Daemon layers and partition your network pool maps:

```bash
# Step 1: Scan for unassigned network hardware block units
sudo ceph orch device ls

# Add empty drives to OSD engines (Replace /dev/sdb with your node's raw block drive string)
sudo ceph orch daemon add osd server1:/dev/sdb
sudo ceph orch daemon add osd server2:/dev/sdb
sudo ceph orch daemon add osd server3:/dev/sdb

# Check OSD operational flags
sudo ceph osd status

# Step 2: Establish a placement-group tracking replication data pool
sudo ceph osd pool create rbd 32 32
sudo ceph osd pool application enable rbd rbd
sudo rbd pool init rbd

# Step 3: Create a 10 Gigabyte raw virtual RBD drive image layer block
sudo rbd create --size 10G rbd/test-volume

# List and print device characteristics
sudo rbd ls rbd
sudo rbd info rbd/test-volume
```

#### Subtask 2.3: Test Ceph Block Storage
Examine block connectivity parameters by mapping, formatting, and writing payload data:

```bash
# Step 1: Map the virtual RADOS block device layer directly into system devices
sudo rbd map rbd/test-volume
rbd showmapped

# Format the system block mapping layer (e.g., target location /dev/rbd0)
sudo mkfs.ext4 /dev/rbd0

# Mount device assets to verify host disk read/write capability
sudo mkdir -p /mnt/ceph-block
sudo mount /dev/rbd0 /mnt/ceph-block

# Verify state consistency
echo "Hello from Ceph block storage" | sudo tee /mnt/ceph-block/test.txt
cat /mnt/ceph-block/test.txt
```

---

### Task 3: Integrate NFS/Ceph with Kubernetes

#### Subtask 3.1: Configure NFS Storage in Kubernetes
Deploy static Persistent Volumes inside your active orchestration cluster framework:

```bash
# Step 1: Write a declarative definition manifest file for your NFS Persistent Volume
cat << EOF > nfs-pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nfs-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: 192.168.1.10  # Replace with actual server1 NFS host controller IP address
    path: /nfs/shared
  persistentVolumeReclaimPolicy: Retain
EOF
kubectl apply -f nfs-pv.yaml

# Step 2: Formulate the consumption matching Persistent Volume Claim descriptor manifest
cat << EOF > nfs-pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nfs-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
EOF
kubectl apply -f nfs-pvc.yaml
kubectl get pvc

# Step 3: Deploy an application using your configured shared network file systems
cat << EOF > nfs-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nfs-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nfs-app
  template:
    metadata:
      labels:
        app: nfs-app
    spec:
      containers:
      - name: app
        image: nginx:latest
        volumeMounts:
        - name: nfs-storage
          mountPath: /usr/share/nginx/html
      volumes:
      - name: nfs-storage
        persistentVolumeClaim:
          claimName: nfs-pvc
EOF
kubectl apply -f nfs-deployment.yaml

# Monitor container creation properties
kubectl get deployments
kubectl get pods
```

#### Subtask 3.2: Configure Ceph Storage in Kubernetes
Pull down the required Ceph CSI components to unlock dynamic storage provisioning capabilities:

```bash
# Step 1: Clone the community Ceph Container Storage Interface configuration repositories
git clone https://github.com/ceph/ceph-csi.git
cd ceph-csi/deploy/rbd/kubernetes

# Register RBAC permissions and provisioner daemon sets inside Kubernetes
kubectl apply -f csi-provisioner-rbac.yaml
kubectl apply -f csi-nodeplugin-rbac.yaml
kubectl apply -f csi-rbdplugin-provisioner.yaml
kubectl apply -f csi-rbdplugin.yaml

# Step 2: Extract environment parameters to declare custom StorageClasses
CLUSTER_ID=$(sudo ceph fsid)
MON_ENDPOINTS=$(sudo ceph mon dump | grep "mon\." | awk '{print $2}' | tr '\n' ',' | sed 's/,$//')
```

---

## 🛠️ Troubleshooting & Configuration Issues

### Issue 1: `exportfs: /etc/exports: syntax error: bad option list`
* **Cause:** NFS cannot parse options due to a space between the client matching pattern and the configuration parameters block.
* **Resolution:** Ensure there are **no spaces** between your destination client string and the opening option character. Use `/nfs/shared *(rw...)` instead of `/nfs/shared * (rw...)`.

### Issue 2: `rbd: map failed: krbd missing module parameters` or connection timeouts
* **Cause:** The kernel lacks the `rbd` storage module, or underlying firewall policies are dropping target block pooling traffic.
* **Resolution:** Run `sudo modprobe rbd` on the target consumer client host to force load the missing system driver options. Check and update local firewall profiles to permit ports `6789` (Monitors) and `6800-7300` (OSDs).

### Issue 3: Kubernetes Persistent Volume Claim hangs in an indefinite `Pending` state
* **Cause:** The NFS endpoint IP address cannot resolve, or access modes (like matching `ReadWriteMany` limits) clash with capabilities configured inside backend system maps.
* **Resolution:** Inspect operational loop errors using `kubectl describe pvc nfs-pvc`. Crosscheck that your target cluster node can reach the host storage controllers using `ping` and validation flags.

---

## 🏁 Conclusion
By implementing this lab guide, you have bridged the architectural design gaps separating file-shared paradigms from low-level distributed environments.

Key takeaways from this exercise include:
* **NAS vs. Distributed Block Storage:** NFS provides easy-to-use file-level sharing, while Ceph distributes raw block layouts, making it suitable for high-performance databases and container filesystems.
* **Storage Cluster Scalability:** Configuring automated OSD device mappings allows storage nodes to expand incrementally without forcing down active mount partitions.
* **Decoupled Cloud Infrastructure:** Utilizing container storage abstractions (PVs and PVCs) keeps your container application deployment lifecycles independent from actual server compute profiles.

---

## 📁 Repository Directory File Structure
To properly maintain your configurations, align your application manifests and localized project code structures to match the following directory layout:

```text
📁 k8s-storage-lab/
├── 📄 README.md                    # Main lab workspace documentation and matrix indexes
├── 📁 manifests/                   # Production kubernetes orchestration definitions
│   ├── 📄 ceph-storageclass.yaml   # StorageClass deployment schema blueprints
│   ├── 📄 nfs-deployment.yaml      # High-availability web applications mounting NFS volumes
│   ├── 📄 nfs-pv.yaml              # Static Network File System Persistent Volume blueprints
│   └── 📄 nfs-pvc.yaml             # Storage allocation claim descriptor schemas
└── 📁 ceph-csi/                    # Task 3.2 cloned upstream repository asset files
    └── 📁 deploy/
        └── 📁 rbd/
            └── 📁 kubernetes/      # Ceph CSI driver RBAC and plugin specifications
```
