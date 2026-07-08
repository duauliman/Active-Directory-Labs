# Windows Server 2025 Home Lab – AD DS, DNS, DHCP & Group Policy

A hands-on home lab where I deployed **Windows Server 2025 (Evaluation)** on a VM, configured it as a domain controller, and used Group Policy to enforce a security restriction on an IT group.


## Lab Overview

| Item | Value |
|---|---|
| OS | Microsoft Windows Server 2025 Datacenter (Evaluation) |
| Hypervisor | VMware Workstation |
| Server Name | WS2026DC0 |
| Domain | locallab.com |
| Static IP | 192.X.X.X /24 (redacted) |
| Roles Installed | AD DS, DNS, DHCP |

## Objectives

1. Deploy a fresh Windows Server 2025 VM and complete initial OS setup
2. Harden basic security posture (firewall on, correct date/time)
3. Assign a static IP address
4. Promote the server to a Domain Controller and configure DNS
5. Configure the DHCP server role
6. Create Organizational Units, users, and a security group in AD
7. Apply a Group Policy Object (GPO) to restrict Control Panel access for the IT group

## Step-by-Step

### 1. Deploy the VM & Install Windows Server 2025
- Created a new VM in VMware Workstation and attached the Windows Server 2025 Evaluation ISO.
- Installed **Windows Server 2025 Datacenter (Desktop Experience)**.
- Set the local Administrator password and completed initial logon.

### 2. Set Date & Time
- Opened **Settings → Time & Language** and set the correct time zone and synced the system clock, since an accurate clock is required before joining/promoting a domain (Kerberos is time-sensitive).

### 3. Enable Microsoft Defender Firewall
- Verified via **Server Manager → Local Server** that Microsoft Defender Firewall is **On** for the Domain profile.
- Left Windows Defender Antivirus real-time protection enabled as well.

### 4. Configure a Static IP Address
- Went to **Network Connections → Ethernet0 → Properties → IPv4**.
- Assigned a static IP, subnet mask, default gateway, and pointed the preferred DNS server at the server itself (required before promoting to a DC).
- *(IP address redacted in screenshots/readme for privacy — shown as `192.168.X.X`.)*

![Server Manager list showing the server registered with IP hidden](images/02-server-manager-list.png)

### 5. Install AD DS Role & Promote to Domain Controller
- In **Server Manager → Add Roles and Features**, installed the **Active Directory Domain Services** role.
- Ran the post-deployment configuration wizard → **Add a new forest** → created the domain `locallab.com`.
- Set the Directory Services Restore Mode (DSRM) password and let the server reboot as the first Domain Controller.

### 6. Configure DNS
- The **DNS Server** role installs automatically with AD DS.
- Verified the forward lookup zone `locallab.com` was created automatically and that the server's own static IP was registered as an A record.

### 7. Install & Configure DHCP
- Installed the **DHCP Server** role via Server Manager.
- Completed **DHCP Post-Deployment Configuration** (authorized the DHCP server in AD).
- Created a new **Scope** with an IP range, subnet mask, default gateway, and DNS server options so client VMs on the lab network receive addresses automatically.

![Server confirmed online and activated](images/03-server-manager-activated.png)

### 8. Create Organizational Unit, Users & Security Group
- Opened **Active Directory Users and Computers (ADUC)**.
- Created an OU named **It**.
- Inside the OU, created:
  - Two user accounts: `Ali Khan` and `sarah ali`
  - One security group: `It groups`
- Added both users as members of the `It groups` security group.

![AD Users and Computers – IT OU with users and group](images/04-ad-users-and-groups.png)

### 9. Create & Link a GPO to Restrict Control Panel
- Opened **Group Policy Management Console (GPMC)**.
- Created a new GPO named **Restrict Control panel**.
- Edited the GPO under:
  `User Configuration → Policies → Administrative Templates → Control Panel`
  and enabled **"Prohibit access to Control Panel and PC settings."**
- Linked the GPO to the **It** OU so it applies to members of the `It groups` security group.
- Confirmed the link in GPMC: Link Enabled = Yes, GPO Status = Enabled, Enforced = Yes.

![GPO linked to the IT OU](images/05-gpo-restrict-controlpanel.png)

### 10. Validate
- Logged in as `sarah ali` on a domain-joined client and confirmed Control Panel access is blocked, verifying the GPO applied correctly through group membership + OU linking.

## Key Takeaways
- Static IP + correct DNS pointer **must** be set before promoting a server to a Domain Controller, or the promotion wizard will complain.
- GPOs apply based on **where the object sits in AD (OU linkage)**, not directly on security group membership — although security group filtering can be layered on top of OU-linked GPOs for more granular targeting.
- `gpupdate /force` on the client (or a reboot) is the fastest way to test policy changes during a lab.

## Tools & Environment
- VMware Workstation
- Windows Server 2025 Datacenter (Evaluation)
- Server Manager, ADUC, GPMC, DHCP/DNS MMC consoles

---
*Note: IP addresses have been redacted from all screenshots and text in this repo for privacy.*
