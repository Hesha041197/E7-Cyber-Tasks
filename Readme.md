# E7 Company Task Documentation

## Table of Contents

- [Environment Requirements](#environment-requirements)
- [Tasks](#tasks)
  - [1. Domain Configuration](#1-domain-configuration)
  - [2. Client Integration](#2-client-integration)
  - [3. Basic Remote Management](#3-basic-remote-management)
  - [4. Documentation](#4-documentation)
- [Network Diagram](#network-diagram)
- [1. Preparing the Environment](#1-preparing-the-environment)
- [2. Setup the Parent Domain](#2-setup-the-parent-domain)
- [3. Setup the Child Domain](#3-setup-the-child-domain)
- [4. Create a Service Account](#4-create-a-service-account)
- [5. Grant Active Directory Read Permissions](#5-grant-active-directory-read-permissions)
- [6. Grant Remote Access Permissions](#6-grant-remote-access-permissions)
- [7. Grant Software Deployment Rights](#7-grant-software-deployment-rights)
- [8. Connect Windows 10/11 to Primary Domain](#8-connect-windows-1011-to-primary-domain)
- [9. Connect Windows 10/11 to Child Domain](#9-connect-windows-1011-to-child-domain)
- [10. Remote Application Installation](#10-remote-application-installation)
- [11. Basic Security Group Policy](#11-basic-security-group-policy)
- [12. Issues Encountered](#12-issues-encountered)
- [Uses and Benefits](#uses-and-benefits)

---

## Environment Requirements

- VMware Workstation / Oracle / Player or equivalent virtualization software
- Windows Server 2019/2022 ISO
- Windows 10/11 ISO

---

## Tasks

### 1. Domain Configuration

- Set up a Windows Server as a primary domain controller.
- Configure a second Windows Server with a child domain.
- Create a service account on the primary domain with:
  - Active Directory read permissions
  - Remote access capabilities
  - Software deployment rights to domain computers

### 2. Client Integration

- Connect at least one Windows 10/11 computer to the primary domain.
- Connect at least one Windows 10/11 computer to the child domain.
- Verify proper communication between all systems.

### 3. Basic Remote Management

- Remotely install a simple application (e.g., Notepad++) to domain computers.
- Set up and test a Group Policy Object that applies a basic security setting.

### 4. Documentation

- Provide screenshots showing successful completion of each major step.
- Include a simple network diagram showing how the systems are connected.
- Document any issues encountered and how they were resolved.

---

## Network Diagram

![Network diagram clarifying the connection](./Screenshots/Network%20Diagram.png)

---

## 1. Preparing the Environment

1. Download and install VMware.
2. Download Windows Server 2019 ISO and Windows 10/11 ISO.
3. Create 2 VMs and mount Windows Server 2019 ISO with required hardware specs.
4. Create 2 VMs and mount Windows 10/11 ISO with required hardware specs.

---

## 2. Setup the Parent Domain

1. Rename the machine.
2. Set a static IP and DNS.
3. Open Server Manager → Manage → Add Roles & Features.
4. Enable "Active Directory Domain Services" and "DNS Server".
5. Install required packages.
6. Promote to domain controller → choose new forest.
7. Set domain name and complete setup.

> ⚠️ A static IP is needed for sustainability.


![Parent domain's server manager](./Screenshots/Parent%20Domain.png)


---

## 3. Setup the Child Domain

1. Rename the machine.
2. Set a static IP.
3. Set DNS as the parent domain IP.
4. Add "Active Directory Domain Services" role.
5. Install required packages.
6. Promote to domain controller → choose existing forest → set as child domain.


![Child domain's server manager](./Screenshots/Child%20Domain.png)


---

## 4. Create a Service Account

1. Server Manager → Tools → Active Directory Users and Computers.
2. Expand domain tab → Right-click "Users" → New → User.
3. Enter details and save.


![Service Account in AD Users](./Screenshots/Service%20Account.png)


---

## 5. Grant Active Directory Read Permissions

1. Right-click domain → Delegate Control → Add service account.
2. Choose "Create a custom task to delegate".
3. Select: "This folder, existing objects..." → tick **Read**.
4. Go to Builtin > Server Operators → add service account.

---

## 6. Grant Remote Access Permissions

### 6.1 Direct Permissions

1. Server Manager → Local Server → Enable Remote Desktop.
2. Untick NLA if necessary.
3. Add user under "Select Users".
4. gpedit.msc → Computer Configuration → Windows Settings → Security Settings → Local Policies → User Rights Assignment → "Allow log on through Remote Desktop Services".

### 6.2 Group Membership (Scalable Approach)

1. AD Users and Computers → Builtin → Remote Desktop Users → Add service account.
2. Enable Remote Desktop in Local Server.
3. Ensure group has "Allow log on..." rights via `gpedit.msc`.


![Remote desktop to parent domain using the service account](./Screenshots/Service%20Account%20RDP.png)


---

## 7. Grant Software Deployment Rights

1. Enable WinRM on the target machine
2. Add the machine that will deploy the software to the trusted hosts on the target machine
3. Add the target machine to trusted hosts on deploying machine
4. Start WinRM session
5. Enter Credentials
6. Copy the installation file to target machine
7. Run the installer


![Powershell commands to install Chrome using WinRM](./Screenshots/Software%20Deployment.png)


---

## 8. Connect Windows 10/11 to Primary Domain

1. Create a user in parent domain AD.
2. Change DNS to parent domain IP.
3. Go to Settings → Accounts → Access Work or School → Connect → Join this device to a local AD domain.
4. Enter domain name and connect.


![Client 1 joined to the parent domain](./Screenshots/Client1%20Domain%20Joined.png)


---

## 9. Connect Windows 10/11 to Child Domain

1. Create a user in child domain AD.
2. Change DNS to child domain IP.
3. Join to domain via same steps as above.


![Client 2 joined to the child domain](./Screenshots/Client2%20Domain%20Joined.png)


---

## 10. Remote Application Installation

1. Create a shared folder accessible to the user.
2. Create a new GPO in Group Policy Management.
3. Set the path to the application installer.
4. On client machine: run `gpupdate /force` → Restart.
5. Confirm installation via Event Viewer.


![Event viewer log showing successful deployment](./Screenshots/Google%20Chrome%20remote%20deployment.png)


![Chrome.exe appearing on the parent DC's desktop](./Screenshots/Google%20Chrome%20installed%20on%20parent.png)

---

## 11. Basic Security Group Policy

1. Create a GPO in Group Policy Management.
2. Enable setting (e.g., restrict pausing Windows updates).
3. On client machine: run `gpupdate /force`.


![GPO view for pausing windows updates](./Screenshots/GPO%20Pause%20windows%20updates.png)


![Windows updates view in settings with pause disabled](./Screenshots/Paused%20Updates.png)

---

## 12. Issues Encountered

**Issue 1**: Unable to RDP into parent domain using service account  
**Cause**: RDP group not added to “Allow log on through Remote Desktop Services”  
**Solution**: Added RDP group in local policy under the same setting

---

## Uses and Benefits

Implementing this task provides foundational skills and practical experience in managing enterprise IT environments. Below are the key uses and benefits:

### 🔐 Centralized User and Resource Management

- Simplifies administrative control over users, groups, and devices.
- Ensures consistent security and policy enforcement across all connected machines.

### 🌐 Real-World Network Setup Practice

- Mirrors real enterprise infrastructure with parent-child domain configurations.
- Teaches practical skills used by system administrators in corporate networks.

### 💼 Service Account Management

- Demonstrates how to create and delegate permissions for service accounts.
- Promotes secure handling of administrative tasks without over-privileging users.

### 📡 Remote Administration

- Enables secure software deployment and remote configuration via Group Policy Objects (GPOs).
- Reduces time and effort required for manual installations on each endpoint.

### 🛡️ Policy Enforcement and Security

- Introduces basic security enforcement via GPOs.
- Prepares systems for compliance with organizational or industry standards.

### 📘 Documentation and Troubleshooting

- Develops documentation habits that are vital for audits, team collaboration, and future maintenance.
- Builds problem-solving skills by encountering and resolving realistic IT issues.
