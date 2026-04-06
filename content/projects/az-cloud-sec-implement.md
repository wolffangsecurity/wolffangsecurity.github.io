# Azure Cloud Security Implementation: IaaS Migration Scenario

## Overview
This project documents a cloud security implementation plan for a 
simulated organization migrating from on-premises infrastructure to 
Microsoft Azure under an Infrastructure as a Service
(IaaS) model. The engagement covers identity 
and access management, encryption, backup configuration, compliance 
mapping, and threat mitigation across a multi-department Azure 
environment.

**Environment:** Microsoft Azure (IaaS)  
**Compliance Scope:** FISMA / FedRAMP, NIST 800-53, PCI DSS  
**Tools Used:** Azure RBAC, Microsoft Entra ID, Azure Key Vault, 
Azure Backup, Microsoft Defender for Cloud, Azure PowerShell  

## Scenario
A mid-sized organization with U.S. government contracts and daily 
payment card transactions is migrating to Azure  following the 
departure of a key IT consultant. The departure exposed gaps in 
access control, backup verification, and vulnerability scanning 
boundaries. Senior leadership requires a documented security plan 
addressing regulatory compliance and risk mitigation before the 
migration is finalized.

The environment consists of three departments: Accounting, Marketing, 
and IT each with dedicated resource groups, virtual machines, 
key vaults, and distinct access requirements.

> **Note:** Screenshots were captured in a provisioned lab environment. 
> Resource and group names reflect the original lab configuration.

## Contents

- Executive Summary
- Proposed Architecture
- Access Control Design
- Encryption Strategy
- Backup Configuration
- Shared Responsibility and Compliance
- Threat Landscape

## Executive Summary
Harwick Defense Solutions, LLC (HDS) is currently transitioning to Microsoft Azure due to constraints with their leased data centers. 
The departure of a consultant has led to security concerns, with unauthorized access and backup verification issues. 
Senior leadership is alarmed about regulatory compliance and cybersecurity threats. 
The proposed course of action involves implementing robust security measures in Azure, addressing role-based access controls (RBAC), encryption, file backups, and shared responsibilities with Microsoft.
Compliance recommendations and threat mitigation strategies are demanded to be outlined in order to minimize risks and ensure a secure cloud solution.

## Proposed Architecture

### Service Model

HDS requires the ability to deploy and control multiple operating 
systems, virtual machines, and custom applications. A hybrid approach 
emphasizing Infrastructure as a Service (IaaS) is appropriate for this 
environment. This model allows HDS full control over the infrastructure 
while leveraging Azure's managed services for specific workloads.

### Regulatory Compliance Scope

HDS must comply with the Federal Information Security Modernization Act 
(FISMA) and the Payment Card Industry Data Security Standard (PCI DSS) 
due to its contracts with the U.S. government and daily card 
transactions. Azure offers compliance certifications and built-in 
features to help meet these regulatory standards.

### Security Benefits

#### Enhanced Security Features
Azure provides built-in security features including identity and access 
management, encryption, threat detection, and compliance monitoring, 
strengthening the overall security posture of the HDS cloud environment.

#### Scalability and Flexibility
Under IaaS, HDS can scale resources up or down on demand, ensuring the 
cloud environment accommodates changing workloads and requirements.

#### Compliance Assistance
Azure's compliance certifications and built-in features reduce the risk 
of non-compliance penalties and support HDS in maintaining a compliant 
cloud posture.

### Security Challenges

#### Data and Asset Visibility
The departure of the consultant resulted in unauthorized access to data 
and assets across departments. Strict access controls and regular audits 
are required to prevent recurrence and ensure data privacy.

#### Backup Verification
The inability to verify file and system backups presents a significant 
risk of data loss. HDS should implement automated backup verification 
and conduct regular audits to confirm backup integrity.

#### Vulnerability Management
Unvalidated vulnerability scanning boundaries leave the Azure environment 
exposed to unaddressed risks. HDS should update scanning boundaries and 
conduct regular vulnerability assessments to identify and remediate 
security gaps.

## Access Control Design

### RBAC Recommendations

#### Accounting and Marketing: Reader Role

The `Accounting_HDS_Users` and `Marketing_HDS_Users` groups should be 
assigned the following built-in role for their respective resource groups:

- **Reader:** View all resources without the ability to make changes.

#### IT: Multiple Built-in Roles

The `IT_HDS_Users` group should be granted the following built-in roles:

- **Contributor:** Grants full access to manage all resources, but does 
  not allow role assignments in Azure RBAC, management of Azure Blueprints 
  assignments, or sharing image galleries.
- **Virtual Machine Contributor:** Create and manage virtual machines, 
  manage disks, install and run software, reset the root user password via 
  VM extensions, and manage local user accounts via VM extensions. It does not 
  grant access to the connected virtual network or storage account, and does 
  not allow role assignments in Azure RBAC.
- **Network Contributor:** Manage networking resources.
- **Storage Account Contributor:** Permits management of storage accounts 
  and provides access to the account key for Shared Key authorization.
- **Key Vault Contributor:** Manage key vaults without the ability to 
  assign roles in Azure RBAC or access secrets, keys, or certificates.

#### Privileged Identity Management (PIM)

Privileged Identity Management (PIM) is a Microsoft Entra ID service that 
enables management, control, and monitoring of access to critical 
resources. Enabling PIM provides an additional layer of control in support 
of the principle of least privilege.

### RBAC Configuration

#### Accounting and Marketing Resource Groups

The following steps apply to both the Accounting and Marketing resource 
groups:

1. Navigate to the target Resource Group
2. Select **Access Control (IAM)**

<img width="975" height="399" alt="image" src="https://github.com/user-attachments/assets/76464974-78ad-40dd-9d2a-7deab535b3f8" />


3. Click **Add Role Assignment**
4. Search for and select **Reader**

  <img width="975" height="400" alt="image" src="https://github.com/user-attachments/assets/31c7e0d8-8167-482c-89f3-31abd792d07b" />


5. Click **Select Members** and search for `Accounting_HDS_Users` or 
   `Marketing_HDS_Users` as applicable

 <img width="975" height="404" alt="image" src="https://github.com/user-attachments/assets/c8992e43-8842-4f36-842d-fcf0635f217c" />


6. Click **Review + Assign**
<img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/953e23fb-baff-4548-812e-6af1cb8effa6" />


7. Use **Check Access** to confirm the group assignment

   <img width="985" height="402" alt="image" src="https://github.com/user-attachments/assets/5a3da5ac-0a9d-46aa-8f17-fe44f1131167" />

   <img width="975" height="406" alt="image" src="https://github.com/user-attachments/assets/5d1b78a1-5c35-4ace-89b0-baa883133195" />


#### IT Resource Group

The following steps apply to the IT resource group:

1. Navigate to `IT-rg`
2. Select **Access Control (IAM)**
3. Click **Add Role Assignment**
4. Search for and assign each of the following roles individually:
   Contributor, Virtual Machine Contributor, Network Contributor, 
   Storage Account Contributor, and Key Vault Contributor  
   *(each role assignment must be completed separately, five total)*
5. For each role, click **Select Members** and search for `IT_HDS_Users`

  <img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/ccf8fca7-0a51-4664-847f-24f3aa63eea5" />

#### PIM Configuration

PIM configuration requires elevated access not available in this 
environment. See the [Microsoft PIM documentation](https://learn.microsoft.com/en-us/azure/active-directory/privileged-identity-management/pim-getting-started) 
for setup guidance.

<img width="975" height="424" alt="image" src="https://github.com/user-attachments/assets/d2ae9980-d65b-464d-a829-d640a62c7f93" />


## Encryption Strategy

### Key Vault Access Configuration

Accounting, Marketing, and IT have each been granted **Key Vault 
Contributor** access to their respective Key Vaults. This role covers 
management plane operations only and does not permit access to secrets, 
keys, or certificates.

1. Navigate to the target Key Vault and select **Access Control (IAM)**

  <img width="975" height="404" alt="image" src="https://github.com/user-attachments/assets/9de37ab5-3742-44af-88d0-7b8ad6aaa572" />


2. Click **Add Role Assignment**
3. Search for and select **Key Vault Contributor**

  <img width="975" height="399" alt="image" src="https://github.com/user-attachments/assets/afe5d073-4dc5-4828-b1c8-ebcdf5213600" />


4. Click **Select Members** and search for the appropriate group  
   (`Accounting_HDS_Users`, `Marketing_HDS_Users`, or `IT_HDS_Users`)

  <img width="975" height="399" alt="image" src="https://github.com/user-attachments/assets/53cceda5-1a0e-4815-9d75-76fc6ac140dc" />


5. Click **Review + Assign**

  <img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/3d28701a-5d09-4782-8210-9b4290466dbc" />


### Disk Encryption

#### Checking Current Encryption Status

Run the following PowerShell commands to check the current encryption 
status of each VM:
```powershell
Get-AzVMDiskEncryptionStatus -ResourceGroupName "Accounting-rg" -VMName "accounting-vm"
Get-AzVMDiskEncryptionStatus -ResourceGroupName "Marketing-rg" -VMName "marketing-vm"
Get-AzVMDiskEncryptionStatus -ResourceGroupName "IT-rg" -VMName "it-vm"
```

<img width="975" height="427" alt="image" src="https://github.com/user-attachments/assets/22401239-89c2-42f0-92e2-f1af36de8613" />


None of the virtual machines were encrypted at the time of assessment.

#### Enabling Disk Encryption

The following steps apply to each VM. The `it-vm` is used as the 
reference example:

1. Navigate to the Key Vault and enable the Access Policy
2. Select **Key Management** from the available templates
3. On the principal selection page, select `it-vm`

<img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/2b315351-14f1-49e2-b1af-21300627cc2c" />
<img width="975" height="405" alt="image" src="https://github.com/user-attachments/assets/d354f0ff-9033-44fb-bb8c-231b409e24fa" />
<img width="975" height="400" alt="image" src="https://github.com/user-attachments/assets/f5dbea9c-2ea5-4bf6-aa26-85dee9e5c605" />


4. Save the Key Vault reference to a variable:
```powershell
$KeyVault = Get-AzKeyVault -VaultName HDS-KeyVault-IT -ResourceGroupName it-rg
```

5. Apply disk encryption:
```powershell
Set-AzVMDiskEncryptionExtension  -ResourceGroupName it-rg -VMName it-vm -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId
```

### Encryption Recommendations

#### Data at Rest
Azure Blob Storage should be encrypted and decrypted using Azure Key 
Vault to ensure end-to-end data security and compliance with applicable 
encryption standards.

#### Data in Transit
Azure Key Vault, used in conjunction with Azure Application Gateway, 
supports data-in-transit encryption by managing TLS/SSL certificates 
for HTTPS communication channels. At runtime, Azure Application Gateway 
retrieves TLS/SSL certificates directly from Key Vault.

## Backup Configuration

### Backup Policy Setup

HDS maintains a single Recovery Services Vault, `HDS-Backup-Vault`, 
managed by the IT resource group.

#### Daily Backup Configuration

1. Navigate to `HDS-Backup-Vault` and click **Backup**
2. Set **Policy sub-type** to **Enhanced** and click **Create a new policy**
3. Configure the policy with the following settings:

   | Setting | Value |
   |---|---|
   | Policy Name | HDS-Backup-Policy |
   | Frequency | Daily |
   | Time | 7:00 PM |
   | Timezone | Eastern Time (US & Canada) |
   | Instant Restore | 3 days |
   | Daily Retention | 45 days |

  <img width="975" height="397" alt="image" src="https://github.com/user-attachments/assets/d9d965ea-b027-4402-ae77-22191ab4dd96" />

4. Click **OK**, then **Add Virtual Machines** and click **Enable Backup**
<img width="975" height="405" alt="image" src="https://github.com/user-attachments/assets/6d93b381-d343-4d17-aa6c-41667484ef07" />
<img width="975" height="405" alt="image" src="https://github.com/user-attachments/assets/e63be821-af77-4d65-bd24-ff8f31619140" />
<img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/4efde134-2d2c-4091-ba43-e726554207cd" />
<img width="975" height="397" alt="image" src="https://github.com/user-attachments/assets/46aa4655-e0a7-4dcb-b985-0ef3d81b6863" />
<img width="975" height="404" alt="image" src="https://github.com/user-attachments/assets/bc585528-ff23-4555-95e3-7153ca2ad512" />



> **Note:** `accounting-vm` and `it-vm` produced validation errors due 
> to unmanaged disks. Each would require migration to managed disks 
> prior to backup enrollment. `marketing-vm` was successfully enrolled.

<img width="975" height="405" alt="image" src="https://github.com/user-attachments/assets/de1cffff-afe1-4cf8-ba52-210c0b035afb" />
<img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/fd115325-172c-4c2b-a560-44a2f71d26a5" />
<img width="975" height="403" alt="image" src="https://github.com/user-attachments/assets/1fe9d12e-9388-4d5a-a27c-fc327ac92d9e" />




#### Retention Policy Expansion

1. Return to `HDS-Backup-Vault` and navigate to **Backup Policies**
2. Select `HDS-Backup-Policy`
3. Add the following retention points:

   | Retention Type | Schedule | Duration |
   |---|---|---|
   | Weekly | Sunday at 7:00 PM | 12 weeks |
   | Monthly | Sunday at 7:00 PM | 60 months |

4. Click **Update**
<img width="975" height="400" alt="image" src="https://github.com/user-attachments/assets/7139c215-ef26-4934-b5f8-b49eb90c7ec0" />
<img width="975" height="405" alt="image" src="https://github.com/user-attachments/assets/db7c2793-3092-4e91-8e38-597839545f52" />



### Backup Explanation

This backup configuration maintains data integrity and availability for 
HDS operations. The daily, weekly, and monthly retention tiers support 
both operational recovery needs and long-term compliance obligations 
under FISMA and PCI DSS.

## Shared Responsibility and Compliance

### IaaS Responsibility Division

Under the IaaS model, HDS retains responsibility for the following:

- Data
- Devices
- Accounts and directory infrastructure
- Applications
- Network controls
- Operating systems

Microsoft Azure is responsible for the physical infrastructure, 
including hardware, networking fabric, and the hypervisor layer.

<img width="975" height="556" alt="image" src="https://github.com/user-attachments/assets/3652c860-53c5-4d41-b68c-f1613d5bad0c" />


### Risk Analysis

#### Data Breaches
- **Risk:** Misconfigurations in security controls such as firewall 
  rules or access control policies may lead to unauthorized access.
- **Impact:** Sensitive data loss, financial penalties, reputational 
  damage, operational disruption, and erosion of customer trust.

#### Service Downtime
- **Risk:** Cloud provider infrastructure outages or failures may 
  cause service disruptions.
- **Impact:** Lost revenue, reduced productivity, negative customer 
  experience, SLA violations, and reputational damage.

#### Data Loss
- **Risk:** Inadequate backup and recovery mechanisms may result in 
  data loss through accidental deletion or failed recovery operations.
- **Impact:** Operational disruptions, compliance violations, financial 
  losses, and potential inability to resume normal operations.

### Compliance Recommendations

Using Microsoft Defender for Cloud, HDS can assign and monitor 
regulatory compliance standards directly within the Azure environment. 
Navigate to `Defender for Cloud > Environment Settings > Edit Settings > Security Policies` and enable the following frameworks:

<img width="975" height="376" alt="image" src="https://github.com/user-attachments/assets/7f6576ad-8ef8-48b4-aaac-d8df32b571e9" />


#### FedRAMP / FISMA
FedRAMP builds upon FISMA and provides a standardized approach to 
security assessment, authorization, and continuous monitoring for cloud 
products and services used across the federal government.

<img width="975" height="335" alt="image" src="https://github.com/user-attachments/assets/3dd15d4f-502c-4f86-8079-b79a2ed24452" />


#### NIST 800-53
NIST 800-53 provides a comprehensive set of security controls and 
guidelines to strengthen the security posture of information systems, 
supporting confidentiality, integrity, and availability.

<img width="975" height="266" alt="image" src="https://github.com/user-attachments/assets/bc6ce298-40a4-4491-a826-71ae6f92aa59" />


#### PCI DSS
PCI DSS protects sensitive cardholder data and secures payment card 
systems against vulnerabilities and threats, ensuring compliant 
processing, storage, and transmission of credit card information.

<img width="975" height="281" alt="image" src="https://github.com/user-attachments/assets/4fb97f2c-1c00-4cf2-92fb-0c0bb43536e3" />


## Threat Landscape

### Threat 1: Unauthorized Access

**Countermeasure: Multi-Factor Authentication (MFA)**  
Enforcing MFA adds a second layer of authentication. Even if 
credentials are compromised, unauthorized access is blocked without 
the second factor.

### Threat 2: Data Loss or Corruption

**Countermeasure: Backup Strategy**  
A robust backup policy covering critical data, combined with regular 
restoration testing, ensures data integrity and availability in the 
event of loss or corruption.

### Threat 3: Service Interruptions

**Countermeasure: High Availability Architecture**  
Designing the cloud environment with redundancy and failover mechanisms 
minimizes downtime and maintains operations during service disruptions.

## Resource Governance

### Initial State

At the time of assessment, most assets were incorrectly consolidated 
within the IT resource group. The Accounting resource group contained 
only the Marketing Key Vault, and the Marketing resource group contained 
only the Accounting Key Vault.

<img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/028d4c19-8beb-44f3-8d68-44099924bddf" />
<img width="975" height="400" alt="image" src="https://github.com/user-attachments/assets/8bf57fb6-6978-48bf-9150-011fcc2724af" />
<img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/1c4bf04e-ade2-4927-b2bc-40b03b9a123e" />


### Asset Remediation

#### IT Resource Group Cleanup

Move the following assets from `IT-rg` to `Accounting-rg`:
- `accounting-vm`
- `accounting-vmNic`
- `accounting-vnet`

1. Select the assets and click **Move > Move to another resource group**
2. Set the destination to `Accounting-rg` and confirm
<img width="975" height="406" alt="image" src="https://github.com/user-attachments/assets/ffbebde8-af59-4166-9ab7-8d2df072921f" />
<img width="975" height="404" alt="image" src="https://github.com/user-attachments/assets/7b203155-86cc-4abc-8e5c-b1fd51e35262" />
<img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/10df08a4-a9fc-4e43-a645-8bca0b010f1c" />
<img width="975" height="400" alt="image" src="https://github.com/user-attachments/assets/082aa3da-b831-4718-92bf-14a367530cce" />
<img width="975" height="398" alt="image" src="https://github.com/user-attachments/assets/cb98d9c1-6bc8-45fb-8122-c3c65ee1b70c" />



Repeat the same process to move the following to `Marketing-rg`:
- `marketing-vm`
- `marketing-vm_OsDisk`
- `marketing-vmNic`
- `marketing-vnet`

<img width="975" height="372" alt="image" src="https://github.com/user-attachments/assets/2b59dbe2-5360-4ef6-b080-402a45b59834" />
<img width="975" height="405" alt="image" src="https://github.com/user-attachments/assets/803e7d43-0b5e-48ad-9f9c-4a67c9ea8738" />
<img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/58600464-91cf-4ac3-90cc-b294f0a185a8" />
<img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/5863594b-d3bc-4426-9293-e1970e3d3966" />
<img width="975" height="371" alt="image" src="https://github.com/user-attachments/assets/027bb023-9f73-4ec2-a0d3-c2530293b9b6" />




#### Key Vault Reassignment

Move `Marketing-KV` from `Accounting-rg` to `Marketing-rg`:
<img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/65b8c6a8-45e4-43c9-ae86-7644019f1b0e" />
<img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/85affb0f-b31a-42c6-9329-8674a59423e6" />
<img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/051368ac-6b1b-497b-9eee-5ec7496b9e56" />
<img width="975" height="400" alt="image" src="https://github.com/user-attachments/assets/d934ab7b-a021-4f7e-b941-c46ea42d026b" />
<img width="975" height="403" alt="image" src="https://github.com/user-attachments/assets/447c910f-dd3c-4bd4-a5c6-a46d0962cdfd" />



Move `Accounting-KV` from `Marketing-rg` to `Accounting-rg`:
<img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/0a38b08b-6a85-4751-8c31-cb2503dc23a5" />
<img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/34c85385-5c1c-436f-a1a2-ff45dc7a86d9" />
<img width="975" height="404" alt="image" src="https://github.com/user-attachments/assets/bec91070-7418-47cd-841b-6f9a950d848b" />
<img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/24d54681-ed9e-4375-bce0-14eb6621adec" />
<img width="975" height="400" alt="image" src="https://github.com/user-attachments/assets/c2270da4-b865-4a12-842a-46b4e96e53b5" />



### Post-Remediation State

All assets are now correctly distributed across their respective 
resource groups.
<img width="975" height="403" alt="image" src="https://github.com/user-attachments/assets/63df743b-8da8-4042-884f-3301b482e89c" />
<img width="975" height="399" alt="image" src="https://github.com/user-attachments/assets/ce7af87f-628d-4233-83aa-981a3aa72d7c" />
<img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/e99db2c8-7616-46c3-97ec-c2a683830b97" />



