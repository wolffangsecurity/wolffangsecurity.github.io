## Global Threat Visualization: Azure Honeynet Mapping
### Honeynet Analysis

In this project, I deliberately configured both Windows and Linux virtual machines with intentional vulnerabilities and exposed them to the internet for 24 hours, creating a honeynet designed to attract and analyze malicious activities.

These misconfigurations invited attackers from around the world, providing me with the opportunity to observe and analyze their techniques. By tracking the geographic origin of each attack, I gained valuable insights into potential threats.

Following this, I fortified the security of the environment and re-exposed the virtual machines to the internet. This allowed me to compare data and evaluate the effectiveness of the implemented security measures.

## Brute Force Attempts

<img src="https://github.com/paRaade/Global-Threat-Visualization-Azure-Honeynet-Mapping/assets/126734769/768802e4-2803-4d42-b9cc-616cd9fe9040" alt="2024-01-06 01_19_24-linux-ssh-auth-fail - Microsoft Azure — Mozilla Firefox">
  
  <img src="https://github.com/paRaade/Global-Threat-Visualization-Azure-Honeynet-Mapping/assets/126734769/309b126f-d963-4704-a864-1cf18944c3c3" alt="2024-01-06 01_09_16-mssql-auth-fail - Microsoft Azure — Mozilla Firefox">
    
   <img src="https://github.com/paRaade/Global-Threat-Visualization-Azure-Honeynet-Mapping/assets/126734769/623b668b-bce2-4eb3-b571-f1e982b55efe" alt="2024-01-06 01_15_58-nsg-malicious-allowed-in - Microsoft Azure — Mozilla Firefox">
  
  <img src="https://github.com/paRaade/Global-Threat-Visualization-Azure-Honeynet-Mapping/assets/126734769/1c5fec52-3a32-48ad-873e-cf197a1e56c9" alt="2024-01-06 01_22_35-windows-rdp-auth-fail - Microsoft Azure — Mozilla Firefox">


## Before Securing Environment

The following is data collected in the insecure environment over the course of 24 hours:

2024-01-05T05:52:03.5049008Z to 2024-01-06T05:52:03.5049008Z
|Metrics| Count |
|--|--|
| Security Events (Windows VMs) |33486  |
|Syslog (Linux VMs) |2715|
| SecurityAlert (Microsoft Defender for Cloud) |3|
| SecurityIncident (Sentinel Incidents) |186|
| NSG Inbound Malicious Flows Allowed |2392|


## **After Securing Environment**

The following is data collected after hardening the environment over the next 24 hours:

2024-01-08T18:37:19.9634111Z to 2024-01-09T18:37:19.9634111Z

|Metrics| Count |
|--|--|
| Security Events (Windows VMs) |11548|
|Syslog (Linux VMs) |0|
| SecurityAlert (Microsoft Defender for Cloud) |0|
| SecurityIncident (Sentinel Incidents) |0|
| NSG Inbound Malicious Flows Allowed |0|



