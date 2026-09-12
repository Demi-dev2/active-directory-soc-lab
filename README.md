# Active Directory SOC Lab

## Overview

This project documents the development of a home Security Operations Centre (SOC) lab designed to simulate a small enterprise environment.

The lab combines Active Directory, a domain-joined Windows endpoint and a Linux-based SIEM server to create an environment where security telemetry can be collected, monitored and investigated.

The project is being built progressively, with configuration decisions, troubleshooting, verification and security monitoring documented throughout the process.

## Objectives

The main objectives of this project are to:

- Build a small enterprise-style virtual network
- Deploy and configure Microsoft Active Directory
- Configure centralised user and computer management
- Join a Windows workstation to an Active Directory domain
- Deploy a dedicated Linux SIEM server
- Configure reliable networking between lab systems
- Collect Windows security telemetry using Wazuh
- Generate realistic security events
- Develop detection and investigation skills
- Document technical decisions and troubleshooting throughout the build

## Skills Developed

This project is designed to develop practical experience with:

- Virtualisation
- Windows Server administration
- Active Directory Domain Services
- Active Directory users, groups and organisational units
- Windows domain authentication
- DNS
- Linux server administration
- TCP/IP networking
- Static IP configuration
- Network troubleshooting
- SSH remote administration
- Windows security logging
- SIEM deployment
- Security monitoring
- Alert investigation
- Detection engineering
- Technical documentation

## Lab Architecture

The current lab architecture consists of three primary virtual machines:

```text
                         VirtualBox NAT Network
                              10.0.2.0/24
                                   │
              ┌────────────────────┼────────────────────┐
              │                    │                    │
              ▼                    ▼                    ▼
          AD-DC01             WIN-CLIENT01          Ubuntu-SIEM
       Windows Server          Windows 11          Ubuntu Server
            2025                  Pro               24.04 LTS
              │                    │                    │
              │                    │                    │
      Active Directory      Domain-joined          Wazuh SIEM
          + DNS               workstation           (planned)
              │                    │                    │
              └──────────────┬─────┘                    │
                             │                          │
                        soclab.local                    │
                             │                          │
                             └──── security telemetry ──┘
                                      (planned)
```

## Systems

### AD-DC01

`AD-DC01` is the Active Directory domain controller for the lab.

Configuration:

- Windows Server 2025 Standard Evaluation
- Active Directory Domain Services
- DNS
- Domain: `soclab.local`
- NetBIOS domain: `SOCLAB`
- Static IP: `10.0.2.10`

The server provides centralised identity, authentication and directory services for the Windows environment.

### WIN-CLIENT01

`WIN-CLIENT01` is the Windows workstation used to simulate a domain-connected enterprise endpoint.

Configuration:

- Windows 11 Pro
- Hostname: `WIN-CLIENT01`
- Joined to `soclab.local`
- Uses `AD-DC01` for domain services and DNS

Test domain users can authenticate to the workstation, allowing realistic Windows authentication and security events to be generated.

### Ubuntu-SIEM

`Ubuntu-SIEM` is the dedicated Linux server that will host the SIEM platform.

Final configuration:

- Ubuntu Server 24.04.4 LTS
- Hostname: `siem-server`
- 4 vCPUs
- 8 GB RAM
- 60 GB virtual disk
- Approximately 58 GB root filesystem
- Static IP: `10.0.2.20/24`
- Gateway: `10.0.2.1`
- Primary DNS: `10.0.2.10`
- Secondary DNS: `8.8.8.8`
- OpenSSH enabled

Wazuh will be deployed on this system to provide security monitoring, event analysis and investigation capabilities.

## Active Directory Structure

The Active Directory environment uses the following organisational structure:

```text
soclab.local
│
└── SOC-Lab
    │
    ├── Users
    │   ├── Alex Morgan (amorgan)
    │   └── Jordan Lee (jlee)
    │
    ├── Groups
    │   └── SOC-Analysts
    │
    └── Workstations
        └── WIN-CLIENT01
```

The test accounts allow different authentication and group-membership scenarios to be generated within the lab.

`amorgan` is a member of the `SOC-Analysts` security group, while `jlee` represents a standard domain user.

No passwords or sensitive credentials are stored in this repository.

## Networking

All virtual machines communicate through a VirtualBox NAT Network using the private network:

`10.0.2.0/24`

Important infrastructure addresses:

| System | Address | Purpose |
|---|---|---|
| AD-DC01 | `10.0.2.10` | Active Directory and DNS |
| Ubuntu-SIEM | `10.0.2.20` | SIEM server |
| WIN-CLIENT01 | DHCP | Domain workstation |
| NAT Gateway | `10.0.2.1` | External network access |

The Ubuntu SIEM server uses a static address so monitored systems can reliably communicate with the SIEM infrastructure.

## Troubleshooting and Engineering Decisions

Troubleshooting and configuration decisions are being documented as part of the project rather than only recording the final working environment.

### Ubuntu Storage

During the initial Ubuntu deployment, the virtual disk contained unused capacity inside the LVM volume group.

The root logical volume was expanded so the available storage could be used by the operating system.

During the later rebuild, the root logical volume was configured during installation to use the available LVM capacity.

### Network Gateway

The initial static Ubuntu network configuration assumed the VirtualBox NAT gateway was `10.0.2.2`.

This allowed communication with the domain controller but external connectivity failed.

The interface was temporarily returned to DHCP and the routing table was examined. This identified `10.0.2.1` as the gateway supplied by the VirtualBox NAT Network.

The static configuration was rebuilt using the verified gateway and connectivity to both the Active Directory server and the internet was confirmed.

### Ubuntu Rebuild

The original SIEM host was built using Ubuntu Server 26.04.

Before Wazuh deployment, the platform requirements were reviewed and the SIEM server was rebuilt using Ubuntu Server 24.04 LTS to provide a supported environment for the planned Wazuh deployment.

The VM resources were also increased to:

- 4 vCPUs
- 8 GB RAM
- 60 GB storage

This ensured the SIEM platform would be deployed on a more appropriate foundation.

### SSH Host Key Change

After rebuilding the Ubuntu server, SSH correctly detected that the host key associated with the previous VM had changed.

The obsolete key was removed from the Windows host's `known_hosts` file and the identity of the rebuilt server was accepted when reconnecting.

This demonstrated how SSH host-key verification can help identify when the identity of a remote system unexpectedly changes.

## Remote Administration

OpenSSH is enabled on `Ubuntu-SIEM`.

Because the VM operates behind the VirtualBox NAT Network, SSH access from the physical Windows host uses port forwarding:

```text
Windows Host
127.0.0.1:2222
       │
       ▼
VirtualBox NAT Port Forwarding
       │
       ▼
Ubuntu-SIEM
10.0.2.20:22
```

The server can be administered from Windows using:

```powershell
ssh -p 2222 demi@127.0.0.1
```

This provides remote command-line administration without requiring direct interaction with the VirtualBox console.

## Project Status

### Current Phase: SIEM Deployment

The core virtual lab infrastructure has been deployed and verified.

Completed:

- Deployed Windows Server 2025 domain controller (`AD-DC01`)
- Created the `soclab.local` Active Directory forest
- Configured Active Directory DNS
- Created organisational units for users, groups and workstations
- Created test domain users
- Created the `SOC-Analysts` security group
- Deployed Windows 11 endpoint (`WIN-CLIENT01`)
- Joined the Windows endpoint to `soclab.local`
- Verified domain-user authentication
- Verified communication with the domain controller
- Deployed Ubuntu Server 24.04 LTS as the SIEM host
- Configured dedicated SIEM server resources
- Configured static networking
- Verified SIEM-to-domain-controller connectivity
- Verified external network connectivity
- Configured and verified SSH remote administration
- Documented configuration and troubleshooting in GitHub

## Next Stage

The next stage is to deploy **Wazuh** on `Ubuntu-SIEM`.

After Wazuh is operational, the Windows domain controller and workstation will be connected as monitored systems.

The project will then move into:

1. Windows security event collection
2. Authentication monitoring
3. Security alert generation
4. Detection testing
5. Event investigation
6. Analysis of suspicious activity
7. Development of custom detection rules where appropriate

## Documentation

Detailed setup documentation is available in the `setup` directory:

- `setup/active-directory.md` — Windows Server and Active Directory deployment
- `setup/ubuntu-siem.md` — Ubuntu SIEM server deployment, networking and SSH configuration

Supporting verification screenshots are stored in the `screenshots` directory.

## Security Notice

This lab is designed for cybersecurity education and defensive security experimentation.

All users, systems and credentials used in the environment are test resources created specifically for the lab. No real credentials are stored in this repository.
