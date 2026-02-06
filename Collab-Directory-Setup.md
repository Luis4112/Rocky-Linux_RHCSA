DAY 0:

# Rocky Linux 9: Systems Administration Lab

This repository documents the technical configuration, logic, and maintenance of my Rocky Linux 9 environment. It serves as a living "Standard Operating Procedure" (SOP) for my infrastructure.

---

## Lab Project 1: Collaborative Permissions & Shared Workspace
**Objective:** Create a secure, group-owned directory where team members can collaborate on files without permission conflicts.

### 1. Architectural Logic
* **Group Management:** Centralized access via the `sysadmins` group to simplify user scaling.
* **Directory Lockdown:** Applied `770` permissions to ensure "Others" (unauthorized users) have zero visibility.
* **Inheritance (The SetGID Bit):** By applying `chmod 2770`, the directory forces all new files to inherit the `sysadmins` group ID. This prevents "Permission Denied" errors when multiple admins edit the same project.

### 2. Implementation Playbook
```bash
# Create the administrative group
sudo groupadd sysadmins

# Build the directory structure (-p ensures parents are created)
sudo mkdir -p /mnt/shares/collab

# Set group ownership
sudo chgrp sysadmins /mnt/shares/collab

# Set permissions: 2 (SetGID) + 7 (Root) + 7 (Group) + 0 (Other)
sudo chmod 2770 /mnt/shares/collab


Lab Project 2: Logical Volume Management (LVM) & Storage Persistence

Objective: Initialize a secondary raw disk and implement a flexible storage hierarchy that survives system reboots.
1. Architectural Logic

    Abstraction Layers: By using LVM instead of standard partitions, the storage becomes elastic. We can add more physical disks to the Volume Group later without unmounting the filesystem.

    Persistence via FSTAB: Manual mounts are volatile. Editing /etc/fstab with the UUID ensures the system remains stable even if the hardware's device path (e.g., /dev/sdb) changes.

2. Implementation Playbook
Bash

# Step 1: Initialize the Physical Volume (PV)
sudo pvcreate /dev/sdb

# Step 2: Create a Volume Group (VG) pool named 'research_vg'
sudo vgcreate research_vg /dev/sdb

# Step 3: Carve a 500MB Logical Volume (LV) named 'data_lv'
sudo lvcreate -L 500M -n data_lv research_vg

# Step 4: Format with XFS and create Mount Point
sudo mkfs.xfs /dev/research_vg/data_lv
sudo mkdir -p /mnt/data
sudo mount /dev/research_vg/data_lv /mnt/data

3. Validation & Persistence

    UUID Mapping: Retrieved the unique ID via blkid /dev/research_vg/data_lv.

    FSTAB Entry: Added the following line to /etc/fstab: UUID=[UUID] /mnt/data xfs defaults 0 0 via vi.

   Ran sudo umount /mnt/data followed by sudo mount -a. The lack of error messages confirmed the configuration was safe for reboot. Rebooted and mount is still untouched.

During the rest of the day, I will brush up
    
