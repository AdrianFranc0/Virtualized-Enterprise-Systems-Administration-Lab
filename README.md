# Virtualized Enterprise Systems Administration Lab

## Executive Summary

This project demonstrates the deployment, configuration, security, and administration of a **virtualized enterprise IT environment** using VMware ESXi, Windows Server 2022, pfSense, Active Directory, Group Policy, and centralized infrastructure monitoring.

A fictional environment, **FalsoTech**, was created to simulate how a systems administrator deploys and manages core infrastructure services across segmented server, client, and management networks.

Rather than focusing on a single technology, this project follows an end-to-end systems administration workflow that includes virtualization, network segmentation, firewall administration, Windows Server deployment, Active Directory Domain Services, DNS, DHCP, Group Policy, workstation management, access control, and infrastructure monitoring.

Throughout the project, VMware ESXi was used to host the virtual infrastructure, pfSense provided routing and firewall enforcement between VLANs, Windows Server provided centralized identity and network services, and Checkmk provided monitoring capabilities.

---

# Technologies Used

| Technology | Purpose |
|------------|---------|
| VMware ESXi 8 | Virtualization hypervisor |
| VMware Workstation Pro | Platform used to host the nested ESXi environment |
| pfSense Community Edition | Inter-VLAN routing, firewalling, and network segmentation |
| Windows Server 2022 | Server operating system for centralized infrastructure services |
| Active Directory Domain Services | Centralized identity and domain management |
| DNS | Internal name resolution |
| DHCP | Centralized client IP address configuration |
| Group Policy | Centralized workstation security configuration |
| Windows 10 Pro | Domain-joined client workstation |
| Ubuntu Server | Dedicated management and monitoring server |
| Checkmk Community | Infrastructure availability and network health monitoring |

---

# Skills Demonstrated

- VMware ESXi Administration 
- Virtual Machine Deployment
- Virtual Network Configuration
- VLAN Segmentation
- pfSense Firewall Administration
- Inter-VLAN Routing
- Windows Server Administration
- Active Directory Administration
- Domain Join Administration
- Group Policy Management
- Workstation Security Hardening
- Least-Privilege Network Access
- Firewall Rule Validation
- Network Troubleshooting
- Linux Server Administration
- Infrastructure Monitoring
- Systems Administration Documentation
---

# VMware ESXi Infrastructure Deployment

## Objective

The first phase of the project focused on establishing the virtualization infrastructure required to host the enterprise lab environment.

VMware ESXi was deployed as the central hypervisor for the environment, providing the platform used to host the firewall, domain controller, client workstation, and management server.

---

## ESXi Environment

The ESXi environment was configured with dedicated virtual machines for the major infrastructure roles:

- **FALSO-PFSENSE01** – Network firewall and router
- **FALSO-DC01** – Windows Server domain controller and infrastructure services
- **FALSO-CLIENT01** – Domain-joined Windows workstation
- **FALSO-MGMT01** – Ubuntu management and monitoring server

A dedicated datastore, **FALSO-DATASTORE01**, was configured to provide storage for the virtual environment.

Separate ESXi networks were also created to support the segmented server, client, and management infrastructure.

<p align="center">
VMware ESXi Infrastructure Overview<br/>
<img src="IMAGE-LINK-HERE" width="950" style="height:auto;" alt="VMware ESXi host overview showing the FalsoTech virtual machines, networks, and datastore."/>
<br /><br />
</p>

---


# Virtual Network Segmentation

## Objective

Following deployment of the ESXi environment, the next phase focused on creating separate virtual networks for servers, workstations, and management systems.

The objective was to prevent all systems from operating within a single flat network and instead create logical network boundaries based on system role.

---

## VLAN Configuration

A dedicated ESXi virtual switch was configured for the internal lab environment.

Separate port groups were created for each network:

- **LAB-Servers – VLAN 10**
- **LAB-Clients – VLAN 20**
- **LAB-MGMT – VLAN 30**
- **LAB-TRUNK – VLAN 4095**

Each virtual machine was connected to its designated ESXi port group based on its role, ensuring server, client, and management systems were placed within their intended VLANs.

The LAB-TRUNK port group was used to pass VLAN traffic to pfSense, allowing pfSense to perform routing and firewall enforcement between the segmented networks.

<p align="center">
ESXi Virtual Network Segmentation<br/>
<img src="IMAGE-LINK-HERE" width="950" style="height:auto;" alt="VMware ESXi port groups showing segmented server, client, and management VLANs."/>
<br /><br />
</p>

---

## Validation

The ESXi virtual network configuration successfully separated infrastructure systems into dedicated server, client, and management networks.

This segmentation established the network boundaries later used by pfSense to control communication between VLANs.

---

# pfSense Network Infrastructure

## Objective

After establishing the virtual network structure, pfSense was deployed to provide Layer 3 routing and firewall enforcement between the segmented VLANs.

The objective was to create centralized control over communication between the server, client, and management networks while maintaining Internet connectivity for authorized systems.

---

## pfSense Deployment

A dedicated pfSense virtual machine, **FALSO-PFSENSE01**, was deployed within VMware ESXi.

The firewall was connected to both the external virtual network and the internal VLAN trunk, allowing pfSense to operate as the routing and security boundary for the lab environment.

<p align="center">
pfSense Firewall Deployment<br/>
<img src="IMAGE-LINK-HERE" width="950" style="height:auto;" alt="pfSense Community Edition successfully deployed as a virtual firewall within VMware ESXi."/>
<br /><br />
</p>

---

## VLAN Interface Configuration

VLAN subinterfaces were configured on pfSense to provide a default gateway for each internal network:

| Network | VLAN | Gateway |
|---------|------|---------|
| Servers | VLAN 10 | 10.10.10.1 |
| Clients | VLAN 20 | 10.10.20.1 |
| Management | VLAN 30 | 10.10.30.1 |

The VLAN interfaces allowed pfSense to route traffic between networks while applying firewall policies to determine which communication should be permitted.

<p align="center">
pfSense VLAN Interface Configuration<br/>
<img src="IMAGE-LINK-HERE" width="950" style="height:auto;" alt="pfSense VLAN interfaces configured for server, client, and management networks."/>
<br /><br />
</p>

---

## Validation

The configured VLAN interfaces established pfSense as the default gateway and routing point between the segmented networks.

This allowed later firewall policies to restrict unnecessary inter-VLAN communication while permitting required infrastructure services.

---

# Windows Server Infrastructure Deployment

## Objective

Following deployment of the network infrastructure, the next phase focused on establishing centralized Windows Server services.

A Windows Server 2022 virtual machine, **FALSO-DC01**, was deployed within the server network to provide identity, name resolution, and IP address management for the environment.

---

## Core Infrastructure Services

FALSO-DC01 was configured with the primary Windows Server roles required by the lab:

- **Active Directory Domain Services (AD DS)**
- **DNS Server**
- **DHCP Server**

A new Active Directory forest and domain named **falso.local** was created.

Active Directory was used to centrally manage users, computers, and organizational units, while DNS provided internal domain name resolution.

DHCP was configured on FALSO-DC01 to centrally distribute network configuration to client systems.

<p align="center">
Windows Server Infrastructure Services<br/>
<img src="IMAGE-LINK-HERE" width="950" style="height:auto;" alt="FALSO-DC01 Server Manager showing Active Directory Domain Services, DHCP, DNS, and File and Storage Services."/>
<br /><br />
</p>

## Validation

FALSO-DC01 successfully operated as the domain controller and centralized infrastructure server for the environment.

Active Directory, DNS, and DHCP services provided the foundation required for domain membership, centralized workstation configuration, and client network services.

---

# DHCP & Client Network Configuration

## Objective

After the domain infrastructure was established, the next phase focused on connecting the Windows client network to the centralized services provided by FALSO-DC01.

The objective was to allow VLAN 20 workstations to automatically receive network configuration from the Windows DHCP server while maintaining VLAN separation.

---

## DHCP Configuration

A DHCP scope was configured on FALSO-DC01 for the VLAN 20 client network.

The scope distributed:

- Client addresses within the **10.10.20.0/24** network
- Default gateway **10.10.20.1**
- DNS server **10.10.10.10**
- DNS domain **falso.local**

Because the DHCP server resides on VLAN 10 while clients reside on VLAN 20, pfSense DHCP Relay was configured to forward DHCP requests from the client network to FALSO-DC01.

---

## Client Network Validation

FALSO-CLIENT01 successfully received its network configuration from the centralized Windows DHCP server.

The workstation received:

- IPv4 address: **10.10.20.100**
- Default gateway: **10.10.20.1**
- DHCP server: **10.10.10.10**
- DNS server: **10.10.10.10**
- DNS suffix: **falso.local**

<p align="center">
VLAN 20 DHCP and Network Configuration<br/>
<img src="IMAGE-LINK-HERE" width="950" style="height:auto;" alt="FALSO-CLIENT01 receiving VLAN 20 network configuration from the Windows Server DHCP service."/>
<br /><br />
</p>

---

## Validation

The successful DHCP lease demonstrated that centralized Windows DHCP services could support clients located on a separate VLAN through pfSense DHCP Relay.

This configuration allowed client systems to receive the correct IP address, gateway, DNS server, and domain information without placing the DHCP server directly within the client network.

---

# Active Directory Domain Integration

## Objective

After validating client network connectivity, FALSO-CLIENT01 was integrated into the falso.local Active Directory domain.

The objective was to establish centralized authentication and workstation administration through the domain controller.

---

## Domain Join

FALSO-CLIENT01 was successfully joined to **falso.local** and registered as an Active Directory computer object.

The workstation was then placed within the **Workstations Organizational Unit**, allowing workstation-specific Group Policy settings to be centrally applied.

<p align="center">
Active Directory Client Integration<br/>
<img src="IMAGE-LINK-HERE" width="950" style="height:auto;" alt="FALSO-CLIENT01 registered within the Workstations Organizational Unit in Active Directory."/>
<br /><br />
</p>

---

# Group Policy & Workstation Security

## Objective

Following domain integration, the next phase focused on applying centralized workstation security settings using Group Policy.

The objective was to establish a baseline security configuration that could be automatically enforced on domain-joined workstations.

---

## Workstation Security Baseline

A Group Policy Object named **GPO - Workstation Security Baseline** was created and applied to the Workstations Organizational Unit.

The security baseline included:

- Windows Defender Firewall enabled
- Microsoft Defender Antivirus maintained as active
- 15-minute machine inactivity lock
- Guest account disabled
- Automatic Windows Updates configured
- Default inbound firewall traffic blocked
- Default outbound firewall traffic allowed

Using Group Policy allows these security settings to be managed centrally rather than manually configuring each workstation.

---

## Group Policy Validation

After the policy was deployed, Group Policy was refreshed on FALSO-CLIENT01.

The `gpresult` utility was then used to verify that **GPO - Workstation Security Baseline** was successfully applied to the workstation.

<p align="center">
Group Policy Deployment Verification<br/>
<img src="IMAGE-LINK-HERE" width="950" style="height:auto;" alt="FALSO-CLIENT01 gpresult output confirming successful application of the workstation security baseline Group Policy."/>
<br /><br />
</p>

---

## Validation

The successful policy result confirmed that FALSO-CLIENT01 was receiving centralized security configuration from Active Directory.

This demonstrated the ability to create, deploy, and verify workstation security policies through Windows Server Group Policy.

---

# Network Access Control & Firewall Security

## Objective

After core domain services were operational, the next phase focused on reducing unnecessary communication between the segmented networks.

The objective was to allow VLAN 20 workstations to access required domain services and the Internet while preventing unrestricted access to the server and management networks.

---

## Least-Privilege Firewall Configuration

pfSense firewall rules were configured for the VLAN 20 client network using a least-privilege approach.

The policy permitted:

- DNS communication with FALSO-DC01
- Required Active Directory services with FALSO-DC01
- Internet access

Direct client access to other internal networks was restricted unless explicitly required.

Required Active Directory communication was permitted using a defined group of domain service ports while unnecessary inter-VLAN traffic remained blocked.

---

## Access Control Validation

FALSO-CLIENT01 was used to validate both permitted and restricted network communication.

Testing confirmed that:

- DNS resolution through FALSO-DC01 succeeded.
- Internet connectivity remained available.
- Group Policy communication with the domain controller succeeded.
- Direct ICMP communication from VLAN 20 to the VLAN 10 server gateway was blocked.
- Direct ICMP communication from VLAN 20 to the VLAN 30 management gateway was blocked.

<p align="center">
VLAN 20 Firewall Policy and Segmentation Validation<br/>
<img src="IMAGE-LINK-HERE" width="1000" style="height:auto;" alt="pfSense VLAN 20 firewall policy with successful DNS, Internet, and Group Policy validation alongside blocked server and management network access."/>
<br /><br />
</p>

---

## Findings

The validation demonstrated that network segmentation did not require completely isolating the client network from infrastructure services.

Instead, pfSense was configured to permit the specific communication required for domain operation while restricting unnecessary direct access to internal networks.

This approach maintains required business functionality while reducing unrestricted communication between security zones.

---

# Infrastructure Monitoring

## Objective

Following deployment and security validation, the final operational phase focused on establishing centralized infrastructure monitoring.

A dedicated Ubuntu Server virtual machine, **FALSO-MGMT01**, was deployed within VLAN 30 to provide an isolated management platform for monitoring infrastructure availability.

---

## Checkmk Deployment

Checkmk Community was deployed on FALSO-MGMT01 and configured to monitor FALSO-DC01.

The monitoring platform provides centralized visibility into host availability and network health from the dedicated management network.

FALSO-DC01 was successfully added as a monitored host and confirmed as reachable within Checkmk.

<p align="center">
Centralized Infrastructure Monitoring<br/>
<img src="IMAGE-LINK-HERE" width="950" style="height:auto;" alt="Checkmk Community monitoring dashboard showing FALSO-DC01 in an UP state."/>
<br /><br />
</p>

---

## Monitoring Validation

Checkmk collected network availability information for FALSO-DC01, including round-trip latency and packet-loss metrics.

Historical graphs provided visibility into network response time and availability over time.

<p align="center">
Infrastructure Monitoring Metrics<br/>
<img src="IMAGE-LINK-HERE" width="950" style="height:auto;" alt="Checkmk monitoring graphs displaying FALSO-DC01 round-trip latency and packet-loss metrics."/>
<br /><br />
</p>

---

## Operational Visibility

The monitoring implementation demonstrates how a dedicated management network can provide centralized visibility into infrastructure availability.

The current monitoring scope focuses on host reachability, latency, and packet loss while establishing a platform that can later be expanded to include additional operating system and service-level monitoring.

---

# Planned Expansion

The current environment provides a functional foundation for additional systems administration scenarios.

Future expansion of the lab may include:

### Backup & Recovery

Implement centralized virtual machine or server backups and perform controlled recovery testing to validate restoration procedures.

### Expanded Infrastructure Monitoring

Extend Checkmk monitoring beyond host availability to include operating system metrics, storage utilization, Windows services, and additional infrastructure components.

---

# Project Outcome

This project successfully demonstrated the deployment, configuration, security, and administration of a segmented virtual enterprise environment.

Throughout the project:

- VMware ESXi was deployed to provide centralized virtualization for the lab infrastructure.

- Dedicated virtual machines were created for firewall, server, workstation, and management roles.

- ESXi virtual networking was segmented using separate server, client, and management VLANs.

- pfSense was deployed to provide routing and firewall enforcement between network segments.

- Windows Server 2022 was configured with Active Directory Domain Services, DNS, and DHCP.

- A centralized DHCP scope and pfSense DHCP Relay were used to provide network configuration across VLAN boundaries.

- FALSO-CLIENT01 was joined to the falso.local domain and organized within the Workstations Organizational Unit.

- Group Policy was used to deploy and validate a centralized workstation security baseline.

- Least-privilege firewall policies allowed required domain and Internet communication while restricting unnecessary inter-VLAN access.

- FALSO-MGMT01 provided a dedicated management environment for centralized Checkmk monitoring.

- Infrastructure availability, latency, and packet-loss monitoring were successfully validated.

By combining virtualization, Windows Server administration, identity services, network segmentation, firewall security, centralized policy management, troubleshooting, and infrastructure monitoring, this project demonstrates practical hands-on experience administering a virtualized enterprise IT environment using workflows commonly performed by systems administrators.
