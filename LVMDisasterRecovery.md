Project: LVM Snapshots and Disaster Recovery

    Objective: Implement a "Rollback" strategy for high-risk system changes.

    Concept: Utilized Copy-on-Write (CoW) snapshots to create a point-in-time state of the lv_docs volume.

    Execution:

        Created a 100MB snapshot of the production volume.
        
        Simulated an accidental deletion of critical documentation.

<img width="912" height="93" alt="Captura de ecrã 2026-02-13 121942" src="https://github.com/user-attachments/assets/81906332-685e-4d3a-b0ca-d9543f7222ed" />


        Performed an offline merge of the snapshot to restore the filesystem state.

    Takeaway: Verified that LVM snapshots provide a near-instantaneous recovery method compared to traditional file-level backups.

  
<img width="962" height="284" alt="Captura de ecrã 2026-02-13 122004" src="https://github.com/user-attachments/assets/22438afb-88cb-4545-b630-8785a5c36e95" />
