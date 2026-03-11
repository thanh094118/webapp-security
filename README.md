# Web Application Security Monitoring Lab

![Lab Architecture](architecture/mermaid-diagram.png)

Simulating web attacks against **DVWA** and analyzing security telemetry using **IDS** and **SIEM**.

## Skills & Technologies
**Security:** Web attack detection, Log analysis, IDS monitoring  
**Networking:** TCP/IP, HTTP traffic analysis  
**Systems:** Linux server environment  
**Tools:** Kali Linux, DVWA, Snort, Splunk, pfSense, Wireshark

## Attack Simulation
Attacks generated from **Kali Linux** against the vulnerable DVWA application:
- SQL Injection testing on input parameters
- Brute-force login attempts on authentication page

## SOC Monitoring
Security investigation workflow:
- Analyze **Apache access/error logs**
- Monitor **Snort IDS alerts**
- Correlate events using **Splunk SIEM**

## Detected
- HTTP query parameters
- Repeated authentication failure
- IDS alerts triggered by web attack signatures

---

**Objective:** Understand how web attacks generate logs, IDS alerts, and SIEM telemetry within a SOC monitoring pipeline.
