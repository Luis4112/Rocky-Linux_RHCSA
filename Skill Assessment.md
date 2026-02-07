## Skill Assessment : Decoupled Service Architecture (LAMP Foundation)

### Objective
Migrate critical service data (HTTPD and MariaDB) from the OS partition to a scalable, multi-disk LVM pool to ensure system stability and data persistence.

### Technical Implementation
* **Storage:** Created `lv_web` and `lv_db` within a Volume Group spanning two physical drives (`/dev/sdb`, `/dev/sdc`).
* **Migration:** Successfully migrated `/var/lib/mysql` data to LVM without data loss by preserving permissions (`cp -rp`) and updating the VFS mount points.
* **Security:** * Implemented SELinux context restoration (`restorecon`) on new mount points.
    * Configured `firewalld` to allow HTTP traffic.
    * Hardened MariaDB via `mysql_secure_installation`.
* **Persistence:** Validated all mounts and service states via `/etc/fstab` and system reboot testing.

command lsblk:

<img width="568" height="343" alt="Captura de ecrã 2026-02-07 111650" src="https://github.com/user-attachments/assets/9333125e-6fbb-461c-bb36-3d75ff879e23" />


using df -h:

<img width="612" height="211" alt="Captura de ecrã 2026-02-07 111823" src="https://github.com/user-attachments/assets/1d41e2a1-3ee7-4eab-b76d-12497063d114" />
