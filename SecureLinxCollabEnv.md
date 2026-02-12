 Project: Secure Linux Collaborative Environment

Overview

This project demonstrates the implementation of a secure, shared workspace for a specific department (Developers) on a Rocky Linux 9 system. It focuses on the Principle of Least Privilege, Group Inheritance, and Granular Sudo Access.

                                  Technical Implementation

1. Identity & Access Management

    Group Creation: Established a developers group to manage departmental access.

    User Provisioning: Created dev_user with a secure shell and home directory, mapped to the primary group and secondary project groups.

    Tool Used: useradd, groupadd, usermod.
   
<img width="700" height="79" alt="Captura de ecrã 2026-02-12 141654" src="https://github.com/user-attachments/assets/d6f51f01-7cd5-46eb-ae5f-dda8be0c88f9" />

3. Shared Workspace & Inheritance (SetGID)

To prevent file ownership conflicts, I implemented a shared directory with a SetGID bit.

    Directory: /opt/dev_project

    Permissions: 2770 (drwxrws---)

    The Logic: By setting the SetGID bit, any file created by any user within this folder automatically inherits the developers group ownership, ensuring all team members can collaborate without manual permission changes.

    <img width="633" height="59" alt="Captura de ecrã 2026-02-12 141743" src="https://github.com/user-attachments/assets/5c1de70c-a613-4116-b6eb-7d4d2da627d1" />

    <img width="778" height="210" alt="Captura de ecrã 2026-02-12 141839" src="https://github.com/user-attachments/assets/9fdf1461-ea54-45d8-8cda-42275ddc79f1" />

   

3. Granular Privilege Escalation (Sudoers)

Instead of granting full root access, I configured the sudoers file via visudo to allow limited, high-privilege actions.

    Authorized: The developers group can run dnf (package management) without a password.

    Restricted: All other root-level commands (e.g., accessing /root/) remain locked.
    
<img width="980" height="258" alt="Captura de ecrã 2026-02-12 141951" src="https://github.com/user-attachments/assets/10e1d239-8a50-49e4-85f4-211d3348b450" />

  
