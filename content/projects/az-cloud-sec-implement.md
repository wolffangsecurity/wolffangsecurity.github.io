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

- [Executive Summary](#executive-summary)
- [Proposed Architecture](#proposed-architecture)
- [Access Control Design](#access-control-design)
- [Encryption Strategy](#encryption-strategy)
- [Backup Configuration](#backup-configuration)
- [Shared Responsibility and Compliance](#shared-responsibility-and-compliance)
- [Threat Landscape](#threat-landscape)

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

   <img width="975" height="399" alt="image" src="https://github.com/user-attachments/assets/c0b3f9d5-2115-48eb-9a2b-48b7f98f11b4" />

3. Click **Add Role Assignment**
4. Search for and select **Reader**

  <img width="975" height="400" alt="image" src="https://github.com/user-attachments/assets/0a5a1c69-16bf-42dd-895e-16bdad9e1c12" />

5. Click **Select Members** and search for `Accounting_HDS_Users` or 
   `Marketing_HDS_Users` as applicable

  <img width="975" height="404" alt="image" src="https://github.com/user-attachments/assets/2085db67-d838-4ae4-9c31-29e1d6bafad0" />

6. Click **Review + Assign**

   <img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/6cf32f36-0cc5-48fa-a8a2-f3a31f9f397a" />

7. Use **Check Access** to confirm the group assignment

   <img width="985" height="402" alt="image" src="https://github.com/user-attachments/assets/2ecc2b99-6cd0-45a9-9a87-c6291b6cca28" />
   <img width="975" height="406" alt="image" src="https://github.com/user-attachments/assets/92bdc11c-fcea-40de-bf38-a281642ed247" />

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

  <img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/5f79d399-a718-4775-aebf-9c6c81ed41b7" />

#### PIM Configuration

PIM configuration requires elevated access not available in this 
environment. See the [Microsoft PIM documentation](https://learn.microsoft.com/en-us/azure/active-directory/privileged-identity-management/pim-getting-started) 
for setup guidance.

<img width="975" height="424" alt="image" src="https://github.com/user-attachments/assets/0fb2a950-e602-4060-b11b-684119ce06ae" />

## Encryption Strategy

### Key Vault Access Configuration

Accounting, Marketing, and IT have each been granted **Key Vault 
Contributor** access to their respective Key Vaults. This role covers 
management plane operations only and does not permit access to secrets, 
keys, or certificates.

1. Navigate to the target Key Vault and select **Access Control (IAM)**

  <img width="975" height="404" alt="image" src="https://github.com/user-attachments/assets/69db2ffd-4420-4ea3-8f00-a4a29711ddcf" />

2. Click **Add Role Assignment**
3. Search for and select **Key Vault Contributor**

  <img width="975" height="399" alt="image" src="https://github.com/user-attachments/assets/1c091fd9-cac7-4437-b436-bda48c34d39c" />

4. Click **Select Members** and search for the appropriate group  
   (`Accounting_HDS_Users`, `Marketing_HDS_Users`, or `IT_HDS_Users`)

  <img width="975" height="399" alt="image" src="https://github.com/user-attachments/assets/3714b602-e3ac-4b61-8540-6af553786db3" />

5. Click **Review + Assign**

  <img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/f453311b-e951-45be-b44c-91b8de8c1f61" />

### Disk Encryption

#### Checking Current Encryption Status

Run the following PowerShell commands to check the current encryption 
status of each VM:
```powershell
Get-AzVMDiskEncryptionStatus -ResourceGroupName "Accounting-rg" -VMName "accounting-vm"
Get-AzVMDiskEncryptionStatus -ResourceGroupName "Marketing-rg" -VMName "marketing-vm"
Get-AzVMDiskEncryptionStatus -ResourceGroupName "IT-rg" -VMName "it-vm"
```

<img width="975" height="427" alt="image" src="https://github.com/user-attachments/assets/a794c838-55d7-4c71-aeb9-a2a0e1125fce" />

None of the virtual machines were encrypted at the time of assessment.

#### Enabling Disk Encryption

The following steps apply to each VM. The `it-vm` is used as the 
reference example:

1. Navigate to the Key Vault and enable the Access Policy
2. Select **Key Management** from the available templates
3. On the principal selection page, select `it-vm`

   <img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/67482ee0-c05f-43e0-9f10-4331fc81763e" />
   <img width="975" height="405" alt="image" src="https://github.com/user-attachments/assets/fbbcfa84-4a43-4990-8256-5ce372b78749" />
   <img width="975" height="400" alt="image" src="https://github.com/user-attachments/assets/b400b812-4058-4f49-bba2-2f03a609b4e3" />

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

  <img width="975" height="397" alt="image" src="https://github.com/user-attachments/assets/104fdccc-ff6b-414c-a69c-8f84ae787167" />

4. Click **OK**, then **Add Virtual Machines** and click **Enable Backup**

   <img width="975" height="405" alt="image" src="https://github.com/user-attachments/assets/7c6ce8b7-d5c5-48b0-b732-eabd5bfba664" />
   <img width="975" height="405" alt="image" src="https://github.com/user-attachments/assets/d089e264-1e99-4095-a352-706738b0ffe6" />
   <img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/b39082fd-7e64-4fd9-8d59-f0b334ab48e6" />
   <img width="975" height="397" alt="image" src="https://github.com/user-attachments/assets/54d81e13-c567-46dc-88d4-42e85aeecc34" />
   <img width="975" height="404" alt="image" src="https://github.com/user-attachments/assets/70736b32-0da1-4cdd-bc91-4a86cb5b146f" />

> **Note:** `accounting-vm` and `it-vm` produced validation errors due 
> to unmanaged disks. Each would require migration to managed disks 
> prior to backup enrollment. `marketing-vm` was successfully enrolled.

  <img width="975" height="405" alt="image" src="https://github.com/user-attachments/assets/4c64382e-3dbb-4d57-941d-a7355db43cb9" />
  <img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/f89237b6-451e-4fe1-8fef-6dabef3bf796" />
  <img width="975" height="403" alt="image" src="https://github.com/user-attachments/assets/c8223c8f-d3b1-4049-8624-5a8e2a67efcd" />

#### Retention Policy Expansion

1. Return to `HDS-Backup-Vault` and navigate to **Backup Policies**
2. Select `HDS-Backup-Policy`
3. Add the following retention points:

   | Retention Type | Schedule | Duration |
   |---|---|---|
   | Weekly | Sunday at 7:00 PM | 12 weeks |
   | Monthly | Sunday at 7:00 PM | 60 months |

4. Click **Update**

   <img width="975" height="400" alt="image" src="https://github.com/user-attachments/assets/1dfdc83a-8d01-4751-835a-e2133adadf5e" />
   <img width="975" height="405" alt="image" src="https://github.com/user-attachments/assets/020cb157-1d3f-49c2-bfd1-b60d8bb4b75e" />

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

<img width="975" height="556" alt="image" src="https://github.com/user-attachments/assets/adaf1efb-2cdc-4d86-93a0-429955369699" />

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

<img width="975" height="376" alt="image" src="https://github.com/user-attachments/assets/4a77c542-c8f7-48dc-8019-18ffa443ef99" />

#### FedRAMP / FISMA
FedRAMP builds upon FISMA and provides a standardized approach to 
security assessment, authorization, and continuous monitoring for cloud 
products and services used across the federal government.

<img width="975" height="335" alt="image" src="https://github.com/user-attachments/assets/8e769646-548a-4814-a103-48cc344e0392" />

#### NIST 800-53
NIST 800-53 provides a comprehensive set of security controls and 
guidelines to strengthen the security posture of information systems, 
supporting confidentiality, integrity, and availability.

<img width="975" height="266" alt="image" src="https://github.com/user-attachments/assets/b5d06197-4646-44cf-9d8d-a0509f6de193" />

#### PCI DSS
PCI DSS protects sensitive cardholder data and secures payment card 
systems against vulnerabilities and threats, ensuring compliant 
processing, storage, and transmission of credit card information.

<img width="975" height="281" alt="image" src="https://github.com/user-attachments/assets/38d606e2-5daa-4d18-b90c-046a0559c6d3" />

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

<img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/4af4368d-c0ac-40bb-82a7-798672716ffd" />
<img width="975" height="400" alt="image" src="https://github.com/user-attachments/assets/f550e2fd-ef58-4d39-98bb-2460b14bc25f" />
<img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/4c884304-5ba4-4aea-ac8e-5b02111e377c" />

### Asset Remediation

#### IT Resource Group Cleanup

Move the following assets from `IT-rg` to `Accounting-rg`:
- `accounting-vm`
- `accounting-vmNic`
- `accounting-vnet`

1. Select the assets and click **Move > Move to another resource group**
2. Set the destination to `Accounting-rg` and confirm

   <img width="975" height="406" alt="image" src="https://github.com/user-attachments/assets/9fc272eb-9754-4018-b43e-605cd08a7f29" />
   <img width="975" height="404" alt="image" src="https://github.com/user-attachments/assets/191c7f74-5da4-449a-acbe-ffed78779cb3" />
   <img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/432f90c7-4c80-447f-9926-c43343cddc7d" />
   <img width="975" height="400" alt="image" src="https://github.com/user-attachments/assets/d1cd39d2-55cc-4cf0-8535-bb5ad8b21c5d" />
   <img width="975" height="398" alt="image" src="https://github.com/user-attachments/assets/73053663-bc1c-4273-8c59-145969afdf33" />

Repeat the same process to move the following to `Marketing-rg`:
- `marketing-vm`
- `marketing-vm_OsDisk`
- `marketing-vmNic`
- `marketing-vnet`

<img width="975" height="372" alt="image" src="https://github.com/user-attachments/assets/2ad0cb9e-447d-4b85-ad65-78d0d70fdf10" />
<img width="975" height="405" alt="image" src="https://github.com/user-attachments/assets/a004c0bd-eb18-4592-be07-0303fe6fdedb" />
<img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/5a340a78-abbb-4258-a8af-07465ed79ef5" />
<img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/9a8258cf-d050-4ddb-8943-f883eaae4d3b" />
<img width="975" height="371" alt="image" src="https://github.com/user-attachments/assets/000d4455-c4a9-4ffe-975b-b48121ba99e9" />



#### Key Vault Reassignment

Move `Marketing-KV` from `Accounting-rg` to `Marketing-rg`:

<img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/9e02d820-64da-4c22-b00c-14cdedee2e32" />
<img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/d6e78e44-bf6d-4ae5-a462-0c8c3dcf69bb" />
<img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/9358c249-4266-4449-b275-df2f2b72bce3" />
<img width="975" height="400" alt="image" src="https://github.com/user-attachments/assets/b90872ff-3b1b-4238-a560-acd0fd63a70a" />
<img width="975" height="403" alt="image" src="https://github.com/user-attachments/assets/29409c69-b88a-4e96-8128-4ec74fa758a1" />

Move `Accounting-KV` from `Marketing-rg` to `Accounting-rg`:

<img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/2285ecd4-50cf-44b2-b2bb-324511117316" />
<img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/26b4df4e-e22f-4131-86d6-b194ef5bc328" />
<img width="975" height="404" alt="image" src="https://github.com/user-attachments/assets/9ca86b38-9177-4070-b09e-2775da0414f1" />
<img width="975" height="401" alt="image" src="https://github.com/user-attachments/assets/d94c4eca-636c-4922-b9a7-91727ddb099e" />
<img width="975" height="400" alt="image" src="https://github.com/user-attachments/assets/2f47d664-1b25-4f0b-834b-22c1ca77b411" />

### Post-Remediation State

All assets are now correctly distributed across their respective 
resource groups.

<img width="975" height="403" alt="image" src="https://github.com/user-attachments/assets/e07c1b60-ea49-4590-b795-d3a36f32b408" />
<img width="975" height="399" alt="image" src="https://github.com/user-attachments/assets/ab89b02e-d42d-47bd-9eca-cf88a72d76dc" />
<img width="975" height="402" alt="image" src="https://github.com/user-attachments/assets/c5208b45-a1bd-4d70-bc89-bf7e703e87ea" />
