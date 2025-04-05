# **SIEM & Enterprise Security Lab 🛡️**

## **🔹 Overview**

This project is designed to **simulate a corporate-style network environment** with multiple machines, servers, and security components. The goal is to create a **realistic and interactive cybersecurity lab** that enables hands-on experience with both **offensive (red team) and defensive (blue team) security concepts** **(but mostly blue team).**

By **building, configuring, and securing** a fully networked environment, this lab serves as a platform to **explore security tools, detect cyber threats, and investigate incidents**—all within a controlled and educational setting. Note that this lab serves as a learning tool for myself and hopefully anyone else who finds this GitHub project. I am no expert so feel free to fork and modify this as much as you want!

------

## **🎯 Project Goals**

🔹 **Network Simulation** – Set up a multi-machine environment mimicking a small enterprise.  
🔹 **Security Hardening** – Implement **firewalls, system configurations, and endpoint protection**.  
🔹 **Log Collection & SIEM Integration** – Forward logs to a SIEM for analysis.  
🔹 **Threat Detection** – Develop **custom detection rules** to catch malicious activity.  
🔹 **Incident Response & Forensics** – Simulate **attacks**, analyze logs, and document findings.  
🔹 **Red vs. Blue Training** – Execute **offensive techniques** and detect them using defensive tools.  

------

## 🛠️ Blue Team Homelab Setup Checklist (Phase 1)

<details>
<summary>Click to expand</summary>

### 🔹 Infrastructure Setup

✅ Configure VLANs + Firewall  
- [ ] Deploy Windows Server  
- [ ] Configure Active Directory Domain  
- [ ] Set up DNS & optionally DHCP  
- [ ] Create AD users  

### 🔹 Endpoint Deployment

- [ ] Deploy Windows 10/11 Workstations  
- [ ] Join to Domain  
- [ ] Create user sessions, simulate real usage  
- [ ] Install Sysmon + SwiftOnSecurity config  

### 🔹 SIEM (Wazuh) Setup

- [ ] Deploy Linux VM (Ubuntu recommended)  
- [ ] Install Wazuh Stack (Wazuh Manager, Elasticsearch, Kibana, Filebeat)  
- [ ] Open ports in firewall  
- [ ] Access Wazuh Dashboard via browser  

### 🔹 Agent Deployment

- [ ] Install Wazuh agent on Windows Server  
- [ ] Install agent on all Windows clients  
- [ ] Register agents with Wazuh Manager  
- [ ] Confirm logs received  

### 🔹 Log Collection Validation

- [ ] View EventLogs, Sysmon events in Wazuh UI  
- [ ] Simulate:  
  - [ ] Failed logins  
  - [ ] PowerShell execution  
  - [ ] RDP usage  
- [ ] Confirm rules fired, alerts generated  

### 🔹 Network Visibility

- [ ] Deploy Suricata or Zeek on dedicated VM  
- [ ] Mirror VLAN traffic to sensor (if possible)  
- [ ] Send logs to Wazuh via Filebeat  

### 🔹 Threat Simulation & Detection

- [ ] Install Atomic Red Team on a test endpoint  
- [ ] Run:  
  - [ ] T1059 – PowerShell execution  
  - [ ] T1110 – Brute-force attempt  
  - [ ] T1055 – Process injection  
- [ ] Confirm alerts appear in Wazuh  
- [ ] Analyze rules, tag with MITRE ATT&CK  

### 🔹 Dashboarding & Alerting

- [ ] Create Kibana dashboards:  
  - [ ] Failed logins  
  - [ ] Top processes  
  - [ ] Alerts by severity  
- [ ] Build custom detection rules in Wazuh  
- [ ] Link rules to techniques  
</details> 

## 🧠 Skill Expansion & Tool Exploration (Phase 2)

<details>
<summary>Click to expand</summary>

### 🔹 Splunk SIEM (Free Tier)

- [ ] Deploy Splunk Free Edition (500MB/day)  
- [ ] Forward logs from one Windows client  
- [ ] Practice SPL (Search Processing Language)  
- [ ] Replicate Wazuh detections in Splunk  
- [ ] Create basic dashboards and alerts

### 🔹 ELK Stack (Standalone)

- [ ] Deploy vanilla ELK (Elasticsearch + Logstash + Kibana)  
- [ ] Forward logs from endpoints or test logs  
- [ ] Create custom ingestion pipelines with Logstash  
- [ ] Visualize data in Kibana  
- [ ] Compare ELK with Wazuh experience

### 🔹 Zeek (Bro) Network Monitoring

- [ ] Install Zeek on a sensor VM  
- [ ] Send VLAN traffic via mirror or tap interface  
- [ ] Analyze:  
  - [ ] DNS logs  
  - [ ] HTTP logs  
  - [ ] SSL, SSH activity  
- [ ] Forward Zeek logs to ELK or Wazuh

### 🔹 Advanced Threat Simulation

- [ ] Use MITRE Caldera for red team automation  
- [ ] Write detection rules based on Caldera activity  
- [ ] Map alerts to MITRE ATT&CK Navigator

### 🔹 Detection Engineering Practice

- [ ] Write Sigma rules based on log observations  
- [ ] Convert Sigma to Wazuh/Splunk detection syntax  
- [ ] Build a “Detection Catalog” in GitHub  
  - [ ] Detection name  
  - [ ] Trigger method  
  - [ ] Log source  
  - [ ] Tool used (Wazuh, Splunk, etc.)  
  - [ ] MITRE ID

</details>

------
## 🚀 **Sections**

1. [**Basic Setup**](https://github.com/alexh200/blue-team-homelab/tree/main/basic-setup/setup.md)
2. [**Firewall Setup**](https://github.com/alexh200/blue-team-homelab/tree/main/firewall-setup/firewall.md)
------

This project is a **continuous work in progress**, evolving as new tools and techniques are added. Feel free to explore, contribute, and test your own security skills!