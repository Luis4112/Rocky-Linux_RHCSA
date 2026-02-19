Objectives Covered: RHCSA 2.2, 2.3, 2.4

1. Emergency Root Access (The rd.break Method)

<img width="1009" height="352" alt="Captura de ecrã 2026-02-19 092817" src="https://github.com/user-attachments/assets/e16d71fd-9170-417e-ae92-a89ca855b183" />


Demonstrated the ability to intercept the boot sequence to perform administrative recovery without a known root password.

    Procedure: Interrupted GRUB2 bootloader, appended rd.break to the kernel parameters.

    Execution:

<img width="701" height="253" alt="Captura de ecrã 2026-02-19 093042" src="https://github.com/user-attachments/assets/dd164809-9897-433c-92a7-9a5039e92183" />

        Remounted /sysroot with read-write permissions (mount -o remount,rw /sysroot).

        Utilized chroot to enter the system jail.

        Reset the root password and initialized an SELinux relabel (touch /.autorelabel) to ensure security context integrity upon reboot.

2. Log Analysis & Authentication Auditing

Analyzed system behavior through log interpretation.

    Security Auditing: Monitored /var/log/secure to identify failed sudo attempts and unauthorized access patterns.

<img width="1090" height="56" alt="Captura de ecrã 2026-02-19 092126" src="https://github.com/user-attachments/assets/006c69d4-237d-42f4-8eb2-461d7f249bca" />


    Kernel Diagnostics: Used journalctl -p err to filter system-critical errors from the binary journal.

<img width="1068" height="123" alt="Captura de ecrã 2026-02-19 092247" src="https://github.com/user-attachments/assets/d6e47a78-33b2-4985-8ccf-1a3d751662cb" />

    
3. Configuring Journal Persistence

By default, RHEL/Rocky journals are volatile (stored in /run). Configured the system to preserve logs across reboots for forensic analysis.



    Configuration: Modified /etc/systemd/journald.conf to set Storage=persistent.
  

<img width="596" height="450" alt="Captura de ecrã 2026-02-19 092431" src="https://github.com/user-attachments/assets/ac37e0fe-334c-4e6b-8750-66f52e9036c8" />

    Verification: Validated that the system now populates /var/log/journal/ with the unique machine-id directory and verified boot history using journalctl --list-boots.

<img width="862" height="80" alt="Captura de ecrã 2026-02-19 092622" src="https://github.com/user-attachments/assets/f642cb75-11ca-4925-a02f-aedab098d1b7" />

    
