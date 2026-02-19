Project: Essential Tooling & System Lifecycle Management
Objectives Covered: RHCSA 1.8, 1.9, 2.1

1. Advanced File Linking (Hard vs. Soft)

Implemented a test environment to validate data persistence across different linking methods.

    Hard Links: Created using ln. Validated that the Inode is shared between the original and the link. Verified that data persists even if the original file descriptor is deleted.

    Soft Links: Created using ln -s. Validated that it functions as a path-based pointer. Verified that deleting the source results in a "dangling" (broken) link.

<img width="960" height="177" alt="Captura de ecrã 2026-02-19 084231" src="https://github.com/user-attachments/assets/c690f272-4a15-48ab-b750-88d968c37d30" />


 Result: Hard link shares Inode with source; Soft link has unique Inode + path pointer which upon deletion of the source data will not persist in them.

<img width="666" height="82" alt="Captura de ecrã 2026-02-19 084402" src="https://github.com/user-attachments/assets/197ca6fe-5b60-4f62-9fb6-e59937bc46a1" />

2. System Lifecycle & Power Management

Practiced graceful system state transitions using systemd targets and the shutdown utility.

    Scheduled Maintenance: Broadcasted warnings to active sessions using shutdown +[time] "[message]".

    Signal Interruption: Practiced canceling scheduled power events using shutdown -c.

<img width="759" height="87" alt="Captura de ecrã 2026-02-19 084447" src="https://github.com/user-attachments/assets/ef776703-9476-4370-b984-d6d90e920aa2" />

    

    Reboots: Executed standard reboots via systemctl reboot and verified system uptime reset.

   <img width="658" height="90" alt="Captura de ecrã 2026-02-19 084548" src="https://github.com/user-attachments/assets/2298f860-6fea-46f6-a850-fbcaebedf12f" />
