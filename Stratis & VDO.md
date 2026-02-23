Modern RHEL 9 Storage Architecture (Stratis & VDO)
Overview

This project demonstrates the implementation of modern, layered storage solutions in RHEL 9/Rocky Linux 9. The objective was to move beyond traditional LVM to implement Thin Provisioning via Stratis and Data Deduplication/Compression via VDO (Virtual Data Optimizer).
Implementation 1: Stratis Layered Storage

Stratis simplifies storage management by managing pools of block devices and providing "flexible" filesystems.
Configuration Steps

    Service Initialization:
    Bash

    sudo dnf install stratisd stratis-cli -y
    sudo systemctl enable --now stratisd

    Pool & Filesystem Creation:
    Bash

    sudo stratis pool create production_pool /dev/sdc
    sudo stratis fs create production_pool data_shares
    
<img width="773" height="144" alt="Screenshot_20260223_113720" src="https://github.com/user-attachments/assets/b6bbf65b-e81d-4c35-9723-1f0e4ada54b0" />

  <img width="1158" height="249" alt="Screenshot_20260223_102642" src="https://github.com/user-attachments/assets/44291bd5-510c-4d02-89df-f0da20d26369" />


Troubleshooting: Boot Persistence

Problem: During initial testing, the system failed to boot and dropped into Emergency Mode.

Root Cause: Stratis is a user-space daemon. The kernel attempted to mount the Stratis filesystem before the stratisd service was active.

Solution: Implemented a systemd dependency in /etc/fstab.
Plaintext

UUID=<FS_UUID> /mnt/stratis_data xfs defaults,x-systemd.requires=stratisd.service 0 0

The actual problem - In the /etc/fstab file, I had forgotten for some reason to add the '-' on x-systemd.requires. It was enough break the mount options and drop me into emergency mode.
<img width="1278" height="677" alt="Untitled" src="https://github.com/user-attachments/assets/427bc4ee-7aef-4406-b10b-81f8a322cba9" />

The solution : 
mount -o remount,rw /
and fixing the /etc/fstab file mistake that I had done.
rebooted, everything ran as it should've had.

Implementation 2: VDO (Virtual Data Optimizer)

VDO provides inline data reduction. In RHEL 9, this is integrated directly into the LVM stack.
LVM-VDO Integration
Bash

# Initializing Volume Group
sudo vgcreate vg_vdo /dev/sdd

# Creating the VDO-enabled Logical Volume
sudo lvcreate --vdo --name lvm_vdo_vol --size 5G --virtualsize 15G vg_vdo

Takeaway on VDO: Resource Constraints

<img width="776" height="199" alt="Screenshot_20260223_114342" src="https://github.com/user-attachments/assets/02c86253-5013-4343-8306-921a83bc0bdd" />

During implementation on a 2GB virtual disk, the VDO creation failed.
Discovery: VDO requires roughly 3GB - 4.7GB of physical space for its "Universal Index" and metadata overhead regardless of the logical size.
Key Takeaway: VDO is unsuitable for small-capacity partitions (<5GB) but highly efficient for large-scale backup repositories where data redundancy is high.

shutdown the system, created a 20gb harddrive and created the vdo successufully.

<img width="1120" height="452" alt="Screenshot_20260223_114503" src="https://github.com/user-attachments/assets/7e463f64-b85e-4b6b-9cda-6fb53478ef55" />

<img width="617" height="72" alt="Screenshot_20260223_114550" src="https://github.com/user-attachments/assets/bcdf1671-1f52-4001-86e6-dff9c753ee92" />

