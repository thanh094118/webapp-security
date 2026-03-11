# Web Application Security Monitoring Lab

![Lab Architecture](architecture/architecture_network.png)

Simulating web attacks against **DVWA** and analyzing security telemetry using **IDS** and **SIEM**.  
<sub><i>using BKCS lab environment: <a href="https://drive.google.com/file/d/1kCTv1g91it2-owfJJbsQAnxw2l6vo1wT/view?usp=sharing">SEED</a></i></sub>  
<sub><i>attacks against the vulnerable web application:
 <a href="https://github.com/digininja/DVWA">[DVWA]</a></i></sub>  
 <sub><i>The simulated attacks relate to common vulnerabilities from
 <a href="https://owasp.org/www-project-top-ten/">[OWASP Top 10]</a></i></sub>
 
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

![security-lab](https://img.shields.io/badge/type-security_lab-red)
![blue-team](https://img.shields.io/badge/focus-blue_team-blue)
![siem](https://img.shields.io/badge/tool-Splunk-orange)
![ids](https://img.shields.io/badge/IDS-Snort-green)
