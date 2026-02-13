Project: Advanced LVM Management & Live Data Migration

    Objective: Orchestrate a zero-downtime data migration from a failing physical disk to a healthy replacement.

    Tools Used: pvcreate, vgcreate, lvcreate, pvmove, vgreduce.

    The Workflow:

        Initialized LVM stack on /dev/sdb and mounted to /mnt/docs.

<img width="664" height="433" alt="Captura de ecrã 2026-02-13 115102" src="https://github.com/user-attachments/assets/70b5b122-f186-47fb-bf3d-0a4109573c9c" />


        Simulated hardware failure on /dev/sdb.

        Expanded the Volume Group vg_projects with a new PV /dev/sdc.

        
<img width="721" height="269" alt="Captura de ecrã 2026-02-13 115214" src="https://github.com/user-attachments/assets/13068bb1-7e60-49fa-83e0-cd009ba36b18" />



        The Migration: Executed pvmove /dev/sdb /dev/sdc to shift data blocks live.

        Decommissioning: Reduced the VG and wiped LVM labels from the "failed" disk.     

<img width="727" height="517" alt="Captura de ecrã 2026-02-13 115305" src="https://github.com/user-attachments/assets/5e6be11e-7958-43b4-adbd-2afc52541386" />
   
    
    Key Insight: Proved that LVM abstraction allows for hardware maintenance without unmounting filesystems or interrupting user access.

