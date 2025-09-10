# Wazuh Setup and Usage Guide

## 📌 Overview
Wazuh is an open-source Security Information and Event Management (SIEM) tool that provides:
- Log analysis  
- File Integrity Monitoring (FIM)  
- Intrusion Detection System (IDS)  
- Threat intelligence integration  
- Incident response and compliance reporting  

This guide documents the setup, configuration, and usage of Wazuh in a lab environment with:
- **Wazuh Server** (10.136.195.223)  
- **Windows 11 Agent** (10.136.195.254)  
- **Kali Linux Attacker** (10.136.195.74)  

---

## ⚙️ Installation

### Wazuh Server (Linux VM)
1. Install Wazuh server:
 
       curl -sO https://packages.wazuh.com/4.x/wazuh-install.sh
       sudo bash wazuh-install.sh -a

2. Start services:

       sudo systemctl enable --now wazuh-manager
---

## Wazuh Agent (Windows 11)

1. Download Wazuh agent MSI installer from:
 
       Wazuh Downloads -- https://wazuh.com/install/

2. Install and configure ossec.conf:  

       <client>
       <server>
          <address>10.136.195.223</address>
          <port>1514</port>
          <protocol>tcp</protocol>
       </server>
       </client>

3. Register the agent with Wazuh server.

---

## 🔐 File Integrity Monitoring (FIM)
Enable and configure in ossec.conf:
    
    <syscheck>
     <disabled>no</disabled>
      <frequency>43200</frequency>
       <directories realtime="yes">C:\Users\Public\</directories>
    <windows_registry>HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run</windows_registry>
    </syscheck>

Detects unauthorized file or registry changes.

Alerts appear in Wazuh Dashboard → Security Events.

---

## Log Monitoring (Apache / IIS)
To monitor DVWA (installed on Windows with XAMPP):
     
    <localfile>
     <location>C:\xampp\apache\logs\access.log</location>
     <log_format>apache</log_format>
    </localfile>

    <localfile>
     <location>C:\xampp\apache\logs\error.log</location>
     <log_format>apache</log_format>
    </localfile>
This lets Wazuh detect web attacks (SQLi, XSS, brute force).

---

## 🛡️ Attack Simulation
Using Kali Linux:
1. Perform SQL Injection on DVWA:
    
       ' OR 1=1 --
2. Perform Brute Force with Hydra:

       hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.136.195.254 http-get /dvwa/login.php
3. Perform XSS:

       <script>alert('XSS')</script>
---

## Wazuh Detection:

1. Alerts generated in

       /var/ossec/logs/alerts/alerts.json on server.

2. Also visible in

       Wazuh Dashboard → Security Events.
---

## 📊 Features Tested

    ✅ Agent connectivity (Windows ↔ Wazuh Server)
  
    ✅ File Integrity Monitoring (FIM)

    ✅ Apache Log monitoring (DVWA attacks)

    ✅ SIEM correlation with attack logs
  
    ✅ Alert visualization in Wazuh Dashboard

---

## 📖 References

Wazuh Documentation

DVWA Setup Guide

XAMPP for Windows
