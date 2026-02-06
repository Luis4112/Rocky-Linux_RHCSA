DAY 1

LVM expansion

Yesterday, I created an LVM using research_vg as my volume group. I created it with 500mbs of LV.

Today I'll simulate an critical alert, that would require me to perform the necessary steps over a "full" LV and add more space without compromising uptime.

I've retraced the steps into creating a mount (meaning that I removed the LV and the mount that and did that all over again.)

Upon reboot, to check if the UUID was set correctly under the fstab:
<img width="641" height="219" alt="Captura de ecrã 2026-02-06 085343" src="https://github.com/user-attachments/assets/f3a8d5cd-1a73-4e88-acbb-4742710c5fff" />

Seems that everything is alright and the mount survives a reboot.

                                                            Project

Expanding the LVM over a critical necessity for more space. This will be a small number - but the command remains the same for any kind of size that we will want.



<img width="960" height="89" alt="Captura de ecrã 2026-02-06 085705" src="https://github.com/user-attachments/assets/7f911fcd-ce35-4002-9799-51fcf41f57b0" />
running the command sudo lvextend -L + SIZE in M or GB will let us resize the LV that we've mounted.

Now let's run  lsblk to check if these changes are actually updated:

<img width="502" height="211" alt="Captura de ecrã 2026-02-06 085901" src="https://github.com/user-attachments/assets/7f4b2979-437f-417c-a93e-696f1765d07b" />


Yup. it did. Now we have 1G. 



We can't just rely on lsblk though. let's check it with another command:
<img width="569" height="82" alt="Captura de ecrã 2026-02-06 090115" src="https://github.com/user-attachments/assets/e9303ccd-c75f-46c7-89e9-24aefc50bea2" />


Using df -h over the the mountpoint /mnt/data, we check that the update is not all there. meaning that there are still steps required for this to actually change 100%.


<img width="569" height="82" alt="Captura de ecrã 2026-02-06 090115" src="https://github.com/user-attachments/assets/aff4e764-c42d-491e-b9ba-e34d17cdf442" />

We've expanded data_lv, but we actually need to also update the mountpoint.
<img width="703" height="210" alt="Captura de ecrã 2026-02-06 090300" src="https://github.com/user-attachments/assets/68354a41-6c0e-4729-b83e-f6e87322e157" />
<img width="610" height="88" alt="Captura de ecrã 2026-02-06 090333" src="https://github.com/user-attachments/assets/e4a71193-0c70-4448-9da4-4032134f50a5" />

With that, we've successfully expanded the LVM storage. 

-------------------------------------------------------------------------------------

                                                       Auditing and Governance:

Project: Forensic Auditing & Privileged Session Recording

Objective: Implement a high-security auditing policy to record and replay administrative (sudo) sessions for forensic analysis and compliance (NIST/PCI-DSS style).
1. Configuration (The "Black Box")

Instead of standard text logging, I configured sudo to capture full TTY I/O. This records everything the user types and everything the server sends back.

    Tool used: visudo

    Key Parameters:

        Defaults log_output: Enables full session recording.

        Defaults iolog_dir: Specifies the secure vault for log storage.
<img width="1104" height="466" alt="Captura de ecrã 2026-02-06 092219" src="https://github.com/user-attachments/assets/d4cb6776-39de-45de-8e52-f31db614164c" />

2. Security Hardening
<img width="738" height="223" alt="Captura de ecrã 2026-02-06 092337" src="https://github.com/user-attachments/assets/e158e72b-97a8-4bb2-9fc3-c56de8cd1763" />

The audit vault was secured so that only the root user can access or replay the recordings: sudo chmod 0700 /var/log/sudo-io



3. Forensic Replay

Using sudoreplay, I can reconstruct any session to see exactly what an administrator did, including their mistakes or unauthorized actions.

Replay Command: sudo sudoreplay /var/log/sudo-io/00/00/0L
<img width="924" height="443" alt="Captura de ecrã 2026-02-06 093935" src="https://github.com/user-attachments/assets/0ec4a86d-25af-4019-a309-d2e2144f82b2" />

This wasn't really satisfactory to me, so i ran a few cat commands: 
<img width="660" height="167" alt="Captura de ecrã 2026-02-06 095201" src="https://github.com/user-attachments/assets/1d0be3f1-620e-40e2-96d7-535623e2a1df" />

Checking for the log yields more human results, and we can see that tester1 actually ran the dnf binary.

So with that, I would be satisfied to say that we can audit the commands that any user could make, in order to verify any potential dangers.

-------------------------------------------------------------------------------------------

Project: Automated Backup Systems & Shell Scripting

Objective: Develop a robust, automated backup solution for critical data using Bash scripting, featuring error handling, exit code verification, and custom logging.
1. The Automation Logic

Instead of manual copies, I authored backup_manager.sh to handle the lifecycle of a data archive.
<img width="1213" height="619" alt="Captura de ecrã 2026-02-06 104647" src="https://github.com/user-attachments/assets/6ba9b7ed-7f4f-42cb-b4d1-9bd5b34e67d8" />

Key Features:

    Dynamic Variables: Uses timestamps to ensure backup files never overwrite each other (e.g., backup_20260206_1030.tar.gz).

    Compression: Utilizes tar with gzip to minimize storage footprint on the /backups partition.

    Error Redirection: Uses 2>> to capture "Standard Error" and pipe it into a dedicated log file for troubleshooting.

2. Error Handling & Exit Codes

    If $? returns 0, the script logs a SUCCESS message.

    If $? returns any non-zero value, it logs an ERROR and stops execution, preventing the false sense of security that a "failed" backup might give.

3. Implementation Evidence

    Script Path: /home/kohta/backup_manager.sh

    Backup Repository: /backups/

    Audit Trail: /var/log/backup_script.log
<img width="660" height="210" alt="Captura de ecrã 2026-02-06 104613" src="https://github.com/user-attachments/assets/1d33d904-4861-4922-8e16-4df6657baad7" />

4. Scheduling & Persistent Automation (Cron)

To ensure data integrity without manual intervention, I integrated the script into the system's Crontab. This transforms the script from a tool into a System Service.

    Command: crontab -e

    Schedule: 0 0 * * * /home/kohta/backup_manager.sh

    Logic: The script is set to trigger every day at exactly 00:00 (Midnight). This ensures that a fresh recovery point is created daily during low-traffic hours.
<img width="1004" height="765" alt="Captura de ecrã 2026-02-06 105311" src="https://github.com/user-attachments/assets/7e716046-8166-4a9f-a0f7-ff039b7a05e2" />


using the command crontab -l:

<img width="506" height="195" alt="Captura de ecrã 2026-02-06 105440" src="https://github.com/user-attachments/assets/2ecf4ab1-e6b3-4130-968a-05c55ae42416" />

cements that the cronjob created should actually be working.


------------------------------------------------------------------------

                                                             Project

Web Infrastructure & Network Security (Firewalld)

Objective: Deploy a secure Nginx web server on Rocky Linux 9, implementing a "Zero Trust" firewall policy by manually installing and configuring firewalld to filter incoming traffic.
1. Service Orchestration (Nginx)

I installed and enabled the Nginx web server to handle HTTP requests.

<img width="1717" height="927" alt="Captura de ecrã 2026-02-06 110435" src="https://github.com/user-attachments/assets/50170a8a-1bdb-4f04-8710-baa51a71261c" />


    Status: Verified that the service was active and running via systemctl, as well by browsing to the ip.

    
<img width="1638" height="834" alt="Captura de ecrã 2026-02-06 110924" src="https://github.com/user-attachments/assets/3894028e-bc19-448d-9e86-712ef7c82a71" />

    

    Customization: Modified the default index.html to verify the server was successfully serving my specific content from /usr/share/nginx/html/.

    <img width="1884" height="560" alt="Captura de ecrã 2026-02-06 111345" src="https://github.com/user-attachments/assets/53754359-c9f7-4ab7-9665-b6f95233dbab" />


2. Network Hardening

Recognizing that the initial installation lacked a local firewall, I manually deployed firewall to implement a "Deny-by-Default" posture.

<img width="795" height="131" alt="Captura de ecrã 2026-02-06 111945" src="https://github.com/user-attachments/assets/bdc3155f-f74d-45b7-9e69-2362fbf86da4" />


    Observation: After starting the firewall, I performed a "Lockout Test." The browser attempts to reach the IP timed out, proving that the service was successfully shielded from the network despite being active locally.

3. Granular Access Control

Instead of disabling security, I applied granular rules to allow only the necessary traffic:

    Command: sudo firewall-cmd --permanent --add-service=http

    Validation: Verified that only Port 80 was opened while maintaining a block on all other unauthorized ports.

4. Traffic Audit

To ensure total visibility, I audited the server logs to confirm my host machine's successful connection.

    Log Location: /var/log/nginx/access.log

    
  Result: Confirmed the source IP, timestamp, and HTTP 200 (Success) status codes for incoming requests.

  
<img width="1559" height="176" alt="Captura de ecrã 2026-02-06 112140" src="https://github.com/user-attachments/assets/e54fc6c0-235e-41f9-8fe4-066ddb11d89c" />

    
