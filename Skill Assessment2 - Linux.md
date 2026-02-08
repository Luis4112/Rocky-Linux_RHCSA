Skill Assessment- Linux - Hardened Infrastructure & Storage Redirection
Project Overview

This project demonstrates the transition from a "Raw" OS state to a Hardened Production Baseline. The focus was on minimizing the attack surface and implementing Storage Redirection via LVM to ensure system resilience and data integrity.
Technical Objectives

    Zero-Trust Hardening: Disabling root entry and implementing session-level security.

    Persistence Engineering: Ensuring all configurations survive hard reboots.

    Blast Radius Control: Isolating high-risk applications (MariaDB) from the OS partition.

Implementation Details
1. The Perimeter (SSH & Identity)

    Port Obfuscation: Migrated SSH from the default port 22 to 2222 to mitigate automated brute-force attempts.

    Policy: Disabled PermitRootLogin to enforce the use of a non-root administrative user under /etc/ssh/sshd_config.

    Legal Compliance: Implemented a mandatory pre-login SSH Banner and post-login MOTD to establish ownership and monitoring notice.
   
<img width="549" height="129" alt="Captura de ecrã 2026-02-08 111216" src="https://github.com/user-attachments/assets/cfed76d2-b565-43a0-b175-dd4245147d4a" />


3. The Shield (Firewall & SELinux)

    Implemented a "Deny-by-Default" firewall policy using firewalld.

    Handled SELinux port labeling to allow the SSH service to bind to a non-standard port, ensuring security policy consistency.

4. Storage Redirection (LVM Architecture)

To prevent "Disk-Full" system failures, the MariaDB heart was redirected:

    Physical: Added a secondary 2GB virtual disk.

    Logic: Created Physical Volume (PV), Volume Group (vg_database), and Logical Volume (lv_mysql).

    Filesystem: Formatted with XFS and mounted directly to /var/lib/mysql.

    Persistence: Configured /etc/fstab for automatic mounting with correct mysql:mysql ownership and SELinux context restoration.

Validation Proof

    Uptime Persistence: All services and mounts verified functional immediately following a system reboot.

    Service Status: MariaDB confirmed as Active (running) with storage verified on the dedicated logical volume via lsblk.

Screenshots : 

lsblk -f command : 


<img width="1095" height="231" alt="Captura de ecrã 2026-02-08 111425" src="https://github.com/user-attachments/assets/0185f756-dcd0-49b4-967a-27a7e0dd3cfc" />

ss -tulpn :

<img width="1281" height="154" alt="Captura de ecrã 2026-02-08 111655" src="https://github.com/user-attachments/assets/d9e8f65a-aaad-445f-9c90-2bb60e0a0fcd" />

SELinux context using ls -ldZ /var/lib/mysql;

<img width="850" height="45" alt="Captura de ecrã 2026-02-08 111858" src="https://github.com/user-attachments/assets/ae84bc29-1864-4bad-b3a2-4e06e0f04547" />


