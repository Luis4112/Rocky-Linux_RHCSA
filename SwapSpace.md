Project: LVM-Based Swap Space Management

    Objective: Dynamically expand system virtual memory (Swap) to prevent OOM (Out Of Memory) failures.

    Reasoning: Used LVM instead of a standard partition to allow for future resizing without repartitioning the disk.

    The Process:

        Created a new Logical Volume lv_swap_ext.

        Wiped legacy XFS signatures to prepare the block device.

        Initialized the swap area using mkswap.

<img width="658" height="62" alt="Captura de ecrã 2026-02-13 123445" src="https://github.com/user-attachments/assets/9d00eca8-cdfa-49bb-95de-0efec5778309" />

        Activated the space live with swapon.

<img width="507" height="79" alt="Captura de ecrã 2026-02-13 123547" src="https://github.com/user-attachments/assets/7245f956-9931-4871-8de5-569d60810198" />

        Configured persistent mounting in /etc/fstab.
<img width="907" height="289" alt="Captura de ecrã 2026-02-13 123524" src="https://github.com/user-attachments/assets/79f162d9-535b-450f-aaf8-07352c673b8b" />


    Key Skill: Demonstrated the ability to manage system resources and memory safety nets.
