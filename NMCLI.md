Networking Persistence and Profile Management (RHCSA 5.1)
 

Objective:
Configure multiple network profiles for a single hardware interface, ensuring specific behaviors (static vs. DHCP) and managing boot-time persistence using NetworkManager.

Theory & Logic
In RHEL 9/10, networking is managed by the NetworkManager daemon. It differentiates between the Device (physical/virtual NIC) and the Connection Profile (the logical configuration).
The Persistence Layer

All configurations are stored as keyfiles in the filesystem. Even if the nmcli tool were removed, these files remain as the "Source of Truth" for the system.

    Storage Path: /etc/NetworkManager/system-connections/

    Format: .nmconnection (INI-style keyfiles)

1. Creating a Static Profile

 created a profile for internal storage traffic that should not start automatically.
Bash

sudo nmcli con add type ethernet con-name static-lab ifname enp0s8 ipv4.addresses 192.168.200.50/24 ipv4.method manual
sudo nmcli con mod static-lab connection.autoconnect no

<img width="1156" height="216" alt="2026-02-23_08-53" src="https://github.com/user-attachments/assets/cc908847-d6c1-4553-891d-ffe988293bc4" />


2. Creating an Emergency DHCP Profile

We created a secondary profile for the same hardware to use in case of migration or troubleshooting.
Bash

sudo nmcli con add type ethernet con-name Emergency-DHCP ifname enp0s8 ipv4.method auto

<img width="1017" height="153" alt="2026-02-23_09-03" src="https://github.com/user-attachments/assets/1ed968cd-d685-48d8-8183-0c22ecfe2a32" />



3. Profile Switching (Hot Swapping)

Verified that bringing one profile UP automatically deactivates the other on the same device.
Bash

sudo nmcli con up Emergency-DHCP  # Interface gets DHCP IP
sudo nmcli con up static-lab      # Interface switches to 192.168.200.50


🔍 Validation & Verification

To ensure the settings are truly persistent and correctly written to disk:

# Check the physical file
sudo cat /etc/NetworkManager/system-connections/static-lab.nmconnection
<img width="1063" height="325" alt="Screenshot_20260223_090524" src="https://github.com/user-attachments/assets/7d7de485-d843-4d41-94de-239e0e316a0b" />

