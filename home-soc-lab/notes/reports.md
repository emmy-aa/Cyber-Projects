# SOC Home Lab Project Report

## Project Title

# Building a Virtual SOC Home Lab Using Wazuh SIEM, Windows Endpoint Monitoring, and Kali Linux

---

## Project Overview

This project involved designing and deploying a virtual Security Operations Center (SOC) home lab environment using VMware Workstation. 
The goal was to build a realistic security monitoring environment where a SIEM platform could collect endpoint telemetry, analyze security events, and provide visibility into suspicious activity.

The completed lab consists of:

* **Wazuh SIEM Server** running on Ubuntu Server 24.04
* **Windows 11 Enterprise Endpoint** running the Wazuh Agent
* **Kali Linux Attack Machine** used for security testing and attack simulation

This project provided hands-on experience with SIEM deployment, endpoint monitoring, Linux administration, Windows security logging, networking, and SOC operations.

---

# Lab Architecture

```
                         Internet
                            |
                       VMware NAT
                            |
              ┌──────────────────────────┐
              │ Ubuntu Server 24.04      │
              │ Wazuh SIEM Server        │
              │ IP: 192.168.203.132      │
              └────────────┬─────────────┘
                           |
                    Wazuh Dashboard
                    Log Collection
                    Security Alerts
                           |
        ─────────────────────────────────
             VMware Host-Only Network
        ─────────────────────────────────
                 |                  |
                 |                  |
     ┌────────────────┐   ┌────────────────┐
     │ Windows 11     │   │ Kali Linux     │
     │ Endpoint       │   │ Attacker       │
     │ Wazuh Agent    │   │ Security Tools │
     │                │   │                │
     │192.168.203.128 │   │192.168.203.129 │
     └────────────────┘   └────────────────┘
```

---

# Environment Details

| System           | Operating System                 | IP Address      | Purpose                                      |
| ---------------- | -------------------------------- | --------------- | -------------------------------------------- |
| Wazuh Server     | Ubuntu Server 24.04              | 192.168.203.132 | SIEM, log management, security monitoring    |
| Windows Endpoint | Windows 11 Enterprise Evaluation | 192.168.203.128 | Endpoint monitoring and telemetry collection |
| Attack Machine   | Kali Linux                       | 192.168.203.129 | Security testing and attack simulation       |
| Hypervisor       | VMware Workstation               | N/A             | Virtual machine management                   |

---

# Project Objectives

The objectives of this SOC home lab were:

* Deploy a functional SIEM platform
* Configure endpoint monitoring
* Collect Windows security telemetry
* Create an isolated security testing environment
* Practice troubleshooting enterprise-like issues
* Develop familiarity with SOC analyst workflows

---

# Phase 1: Virtual Lab Setup

## VMware Configuration

The lab environment was created using VMware Workstation.

Virtual machines created:

1. Ubuntu Server 24.04

   * Role: Wazuh SIEM Server

2. Windows 11 Enterprise Evaluation

   * Role: Security monitoring endpoint

3. Kali Linux

   * Role: Attacker/security testing workstation

---

## Network Configuration

The lab used two virtual network configurations:

### VMware NAT Network

Used for:

* Internet access
* Package downloads
* System updates

### VMware Host-Only Network

Used for:

* Communication between lab machines
* Isolated SOC environment
* Attack simulation

Configured network:

```
192.168.203.0/24
```

Final IP assignments:

```
Ubuntu Wazuh Server:
192.168.203.132

Windows Endpoint:
192.168.203.128

Kali Linux:
192.168.203.129
```

---
[Screenshots of the configuration for each VM]

(https://github.com/emmy-aa/Cyber-Projects/blob/main/home-soc-lab/screenshots/infrastructure/06-kali-vm-configuration.png)

(https://github.com/emmy-aa/Cyber-Projects/blob/main/home-soc-lab/screenshots/infrastructure/07-ubuntu-vm-configuration.png)

(https://github.com/emmy-aa/Cyber-Projects/blob/main/home-soc-lab/screenshots/infrastructure/08-windows-vm-configuration.png)

# Phase 2: Ubuntu Server and Wazuh Deployment

## Ubuntu Server Installation

Ubuntu Server 24.04 was installed as the foundation for the SIEM server.

Resources allocated:

* RAM: 8 GB
* CPU: 4 cores
* Storage: 40 GB

---

## Wazuh Installation

The Wazuh installation script was downloaded and executed:

```bash
curl -sO https://packages.wazuh.com/4.12/wazuh-install.sh

sudo bash ./wazuh-install.sh -a
```

The installation deployed:

* Wazuh Manager
* Wazuh Indexer
* Wazuh Dashboard
* Filebeat

---

# Phase 3: Wazuh Installation Troubleshooting

During deployment, several issues were encountered and resolved.

---

## Issue 1: DNS Resolution Failure

### Error:

```
Could not resolve host packages.wazuh.com
```

### Cause:

The Ubuntu VM did not have proper internet connectivity.

### Solution:

* Added a second VMware network adapter
* Configured NAT connectivity
* Updated network configuration
* Verified DNS resolution

---

## Issue 2: Network Interface Failure

### Error:

```
ens37 is DOWN
```

### Cause:

The second network adapter was not configured in Netplan.

### Solution:

Updated Netplan configuration:

```yaml
network:
  version: 2
  ethernets:
    ens33:
      dhcp4: true

    ens37:
      dhcp4: true
```

Applied changes:

```bash
sudo netplan apply
```

---

## Issue 3: Wazuh Dashboard Installation Failure

### Problem:

The initial Wazuh installation failed during dashboard deployment.

### Cause:

Insufficient system resources.

### Solution:

VM resources were increased:

Before:

* Approximately 4 GB RAM

After:

* 8 GB RAM
* 4 CPU cores

The failed installation was removed using the Wazuh uninstall option, and the installation was completed successfully.

---

# Phase 4: Wazuh Dashboard Deployment

After successful installation, the Wazuh Dashboard was accessed through a web browser.

Dashboard URL:

```
https://192.168.203.132
```

The Wazuh dashboard provided:

* Security event monitoring
* Agent management
* Threat hunting capabilities
* Log visualization

---

# Phase 5: Windows Endpoint Integration

The Windows 11 virtual machine was configured as a monitored endpoint.

The Wazuh Agent was installed successfully.

Verification command:

```powershell
Get-Service WazuhSvc
```

Result:

```
Status      Name
------      ----
Running     WazuhSvc
```

(https://github.com/emmy-aa/Cyber-Projects/blob/main/home-soc-lab/screenshots/wazuh/01-wazuh-running%20.png)

The Windows endpoint successfully appeared in the Wazuh Dashboard as an active agent.

---
[Wazuh Dasboard]

(https://github.com/emmy-aa/Cyber-Projects/blob/main/home-soc-lab/screenshots/wazuh/05-wazuh-dashboard.png)

[Wazuh Dashboard for Mitre Attack]

(https://github.com/emmy-aa/Cyber-Projects/blob/main/home-soc-lab/screenshots/wazuh/02-wazuh-mitre-attack-dashboard.png)

[Wazuh Dashboard for Vulnerability Detection]

(https://github.com/emmy-aa/Cyber-Projects/blob/main/home-soc-lab/screenshots/wazuh/09-wazuh-dashboard-vulnerability-detection.png)

# Phase 6: Kali Linux Deployment

Kali Linux was installed as the attacker workstation.

Purpose:

* Security testing
* Network scanning
* Attack simulation
* Generating security events for analysis

Tools available:

* Nmap
* Wireshark
* Tcpdump
* Netcat
* Other penetration testing utilities

---

# Validation and Testing

The completed lab was validated by confirming:

## Wazuh Server

* Wazuh Manager running
* Wazuh Dashboard accessible
* Indexer operational

## Windows Endpoint

* Wazuh Agent installed
* Agent service running
* Endpoint connected to Wazuh

## Network Connectivity

Verified communication between:

```
Kali Linux
      |
      |
Windows Endpoint
      |
      |
Wazuh Server
```

---

# Skills Demonstrated

This project demonstrates practical experience with:

* Security Operations Center (SOC) concepts
* SIEM deployment and management
* Wazuh administration
* Endpoint monitoring
* Windows event collection
* Linux server administration
* VMware virtualization
* Network troubleshooting
* DNS troubleshooting
* Virtual network configuration
* Security monitoring workflows

---

# Challenges and Lessons Learned

Building this lab provided experience troubleshooting realistic infrastructure issues.

Key lessons:

* Proper network configuration is critical for security tools
* DNS failures can prevent software deployment
* SIEM platforms require adequate system resources
* Troubleshooting is a major part of cybersecurity operations
* Understanding networking improves security analysis

---

# Future Improvements

Future enhancements planned for this SOC lab:

* Install Sysmon on Windows for enhanced telemetry
* Perform MITRE ATT&CK simulations
* Create detection engineering exercises
* Analyze Wazuh alerts from simulated attacks
* Add Active Directory environment
* Add additional endpoints
* Integrate additional security tools such as:

  * Zeek
  * Suricata
  * TheHive
  * Cortex

---

# Conclusion

This project successfully created a functional SOC home lab capable of monitoring endpoint activity and collecting security telemetry.

Through deploying Wazuh, integrating a Windows endpoint, and configuring Kali Linux as an attack workstation, this lab provided hands-on experience with the technologies and workflows used by SOC analysts.

The project demonstrates the ability to build, troubleshoot, and operate a security monitoring environment from the ground up.
