# Active Directory SOC Lab

A home cybersecurity lab designed to simulate a small Windows domain environment and, ultimately, a Security Operations Centre (SOC) monitoring workflow.

The project combines Windows Server, Active Directory, Windows endpoints and an Ubuntu-based SIEM server to provide hands-on experience with identity management, security monitoring, detection and investigation.

## Project Status

**Current Phase:** Active Directory infrastructure complete — SIEM deployment next.

The lab currently has a functioning Active Directory domain with a Windows Server 2025 domain controller and a domain-joined Windows 11 workstation.

The next phase will deploy the SIEM and begin collecting security telemetry from the Windows environment.

## Current Architecture

```text
                         soclab.local
                              │
                    ┌─────────▼─────────┐
                    │     AD-DC01       │
                    │ Windows Server    │
                    │ AD DS / DNS       │
                    │ 10.0.2.10         │
                    └─────────┬─────────┘
                              │
                     Domain Authentication
                              │
                    ┌─────────▼─────────┐
                    │   WIN-CLIENT01    │
                    │ Windows 11 Pro    │
                    │ Domain Workstation│
                    └───────────────────┘


                    ┌───────────────────┐
                    │    Ubuntu-SIEM    │
                    │  Ubuntu Server    │
                    │ SIEM deployment   │
                    │      next         │
                    └───────────────────┘
```

## Active Directory Structure

```text
soclab.local
│
├── Domain Controllers
│   └── AD-DC01
│
└── SOC-Lab
    ├── Users
    │   ├── Alex Morgan (amorgan)
    │   └── Jordan Lee (jlee)
    │
    ├── Groups
    │   └── SOC-Analysts
    │       └── amorgan
    │
    └── Workstations
        └── WIN-CLIENT01
```

## Completed

### Ubuntu Server Foundation

- Deployed Ubuntu Server 26.04.1 LTS
- Configured the `siem-server` hostname
- Installed and enabled OpenSSH
- Verified networking, storage and SSH operation
- Troubleshot an initially inactive SSH service

### Windows Server & Active Directory

- Deployed Windows Server 2025
- Configured `AD-DC01` with a static IPv4 address
- Installed Active Directory Domain Services
- Created the `soclab.local` forest and domain
- Configured DNS and Global Catalog services
- Created dedicated OUs for users, groups and workstations
- Created fictional domain users for testing
- Created the `SOC-Analysts` security group

### Windows Workstation

- Deployed Windows 11 Pro as `WIN-CLIENT01`
- Configured the workstation to use the domain controller for DNS
- Troubleshot VirtualBox network communication between the lab systems
- Joined `WIN-CLIENT01` to `soclab.local`
- Successfully authenticated using the `amorgan` domain account
- Verified `AD-DC01` as the workstation's logon server
- Organised the workstation inside the Active Directory `Workstations` OU

## Troubleshooting

The project includes documentation of issues encountered during deployment rather than only documenting the final working configuration.

Examples so far include:

- OpenSSH being installed but initially inactive on the Ubuntu server
- VirtualBox's initial NAT configuration preventing the Windows virtual machines from communicating as required
- Moving the Windows systems onto a shared NAT Network and verifying connectivity before joining the domain

## Next Phase

The next stage will focus on the SOC monitoring environment:

1. Connect `Ubuntu-SIEM` to the shared lab network
2. Deploy and configure the SIEM
3. Connect Windows systems to the SIEM
4. Collect Windows security telemetry
5. Generate controlled security events
6. Develop and test detections
7. Investigate security events using the collected telemetry

## Documentation

Detailed build documentation is available in the [`setup`](setup/) directory:

- [`Ubuntu SIEM Server Setup`](setup/ubuntu-siem.md)
- [`Windows Server & Active Directory Setup`](setup/active-directory.md)
- [`Windows Domain Workstation Setup`](setup/windows-client.md)

Supporting screenshots are stored in the [`screenshots`](screenshots/) directory.

## Project Goals

The goal of this project is to develop practical experience with:

- Windows Server administration
- Active Directory and centralised identity management
- Windows domain authentication
- DNS and network configuration
- Linux server administration
- SIEM deployment and administration
- Security log collection
- Detection engineering
- Security event investigation
- Technical troubleshooting
- Cybersecurity documentation

## Disclaimer

This project is conducted entirely within an isolated virtual lab environment using fictional users and test systems. Security testing and event generation are performed only against systems within the lab.
