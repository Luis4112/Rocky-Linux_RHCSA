Objective

To finalize advanced filesystem security (ACLs/SetGID) and establish control over the system's initialization manager (systemd). This project demonstrates the ability to troubleshoot complex permission "denied" scenarios and manage service persistence.


Part 1: The Permissions Troubleshooting

Concept: Resolving the "Capital S" SetGID issue and path traversal blocks.

    The Problem: A user in the correct group could not access a directory due to missing execute bits on the parent and the SetGID bit being "unsupported" by an underlying lack of owner permissions.

    The Fix: Aligning UGO (User/Group/Other) bits with ACL masks and refreshing the user shell session to recognize group membership.


<img width="534" height="101" alt="Captura de ecrã 2026-02-14 093231" src="https://github.com/user-attachments/assets/3e9e97d3-d659-42fa-b3ec-6e92d6d6d18b" />


    Part 2: Systemd Service Control

Concept: Controlling the "Nervous System" of the OS.

    What is it: Systemd (PID 1) manages units. I focused on Services (daemons) and targets (boot states).

    The Logic: * Active: Running in the current session.

        Enabled: Configured to start automatically on boot.

        <img width="1079" height="399" alt="Captura de ecrã 2026-02-14 093021" src="https://github.com/user-attachments/assets/b114a9a0-1b98-4408-b1e5-8378be23a992" />



Part 3: Advanced Unit Masking

Concept: Absolute service suppression.

    The Action: Masked chronyd.service to prevent accidental starting.

    The Result: Symlinked the unit to /dev/null, making it impossible to trigger manually or by dependency.


<img width="634" height="98" alt="Captura de ecrã 2026-02-14 093410" src="https://github.com/user-attachments/assets/4be20183-9022-4309-aa03-f1afa8228fa1" />




    Part 4: Boot Target Management

Concept: Defining the system's operational state.

    The Action: Configured the system to default to multi-user.target (CLI mode), the standard for enterprise servers.

<img width="1087" height="65" alt="Captura de ecrã 2026-02-14 093559" src="https://github.com/user-attachments/assets/55c3bc29-5e9e-425e-91bc-656f4c59f1a3" />

  
