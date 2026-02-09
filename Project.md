Project 2: Architecting a Decoupled & Hardened LAMP Stack

                      The Objective

To move beyond a "standard" web server installation by creating a multi-tenant, high-availability storage architecture. The goal was to isolate the Operating System, the Web Assets, and the Database into independent, elastic containers using LVM (Logical Volume Management) and enforcing Mandatory Access Control via SELinux.
Phase 1: Storage Layering & Abstraction

Instead of a single partition, I implemented a tiered storage strategy across three physical disks. This prevents a "disk full" error in one service from crashing the entire OS.

    Disk A (sda): OS Root and Swap.

    Disk B (sdb): Dedicated to the MariaDB Vault (lv_mysql).

    Disk C (sdc): Dedicated to the Web Gateway (lv_web).

<img width="629" height="250" alt="Captura de ecrã 2026-02-09 092705" src="https://github.com/user-attachments/assets/61399eb2-775b-4650-9ce3-73272bda2b4b" />


    Key Achievement: Successfully performed a Live Online Expansion of the lv_web volume. Increased storage from 1GB to 1.5GB and grew the XFS filesystem without unmounting or dropping web requests.

Phase 2: The Hardened Web Gateway

I deployed Apache (httpd) and PHP-FPM as separate services. By using PHP-FPM, the web processing is decoupled from the web server, allowing for better resource management and security.

    Security Contexts: Managed SELinux policies to allow Apache to serve content from a non-standard LVM mount.

    Permissions: Strictly enforced apache:apache ownership on the web root with httpd_sys_content_t labeling.

<img width="895" height="46" alt="Captura de ecrã 2026-02-09 092913" src="https://github.com/user-attachments/assets/e61f1cca-03fc-4fdb-bf59-7724c527d627" />

Phase 3: The Secure Handshake

The final step was bridging the two isolated storage environments. I established a secure connection between the Web Volume and the Database Volume using a "Least Privilege" database user.

    The Bridge: Created a specialized web_user in MariaDB restricted to a single database.

    The Kernel Flip: Manually toggled the SELinux boolean httpd_can_network_connect_db to authorize the process communication.
    
    <img width="699" height="147" alt="Captura de ecrã 2026-02-09 093011" src="https://github.com/user-attachments/assets/1777c425-e644-46c6-8572-7c7a69dfc943" />

Technical Stack & Skills Demonstrated

    OS: Rocky Linux 9.7 (Blue Onyx)

    Storage: LVM (Physical Volumes, Volume Groups, Logical Volumes, XFS)

    Security: SELinux (Contexts, Booleans, Restorecon), Firewalld

    Services: Apache, MariaDB, PHP-FPM

    Expertise: Live Filesystem Resizing, Service Decoupling, Permission Management
