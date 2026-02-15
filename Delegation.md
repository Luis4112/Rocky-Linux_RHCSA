Project : Granular Access Control & SSH Hardening

    Objective: Delegate specific administrative tasks to non-root users and lock down the SSH attack surface.

    Key Achievements:

        Sudo Delegation: Created a custom sudoers policy in /etc/sudoers.d/ to allow the user (clara) to restart the SSH service without a password, adhering to the principle of Least Privilege.
       
      
<img width="1087" height="65" alt="Captura de ecrã 2026-02-14 093559" src="https://github.com/user-attachments/assets/8adb8c85-77fa-4f06-bb22-e7abd0b74a1e" />


        SSH Whitelisting: Configured AllowUsers to explicitly define which accounts can access the system, preventing unauthorized lateral movement.

<img width="525" height="61" alt="Captura de ecrã 2026-02-15 092827" src="https://github.com/user-attachments/assets/c0f540fa-7a1b-4773-9c32-97e501d45566" />


        Validation Workflow: Utilized sshd -t for configuration pre-flight checks and visudo -f for policy integrity.
