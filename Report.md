# System Report

## Table of Contents

1. [Introduction](#1-introduction)
2. [Initial System Setup](#2-initial-setup)
3. [System Hardening](#3-system-hardening)
4. [System Services](#4-additional-services)
5. [Introduced Vulnerabilities](#5-introduced-vulnerabilities)
6. [Testing and Verification](#6-testing-and-verification)
7. [Conclusion](#7-conclusion)
8. [Appendix](#appendix)

## 1. Introduction

This project involves setting up and securing a vulnerable Flask web application on a Linux server. The initial tasks include installing the Flask server and addressing critical security flaws, such as SQL injection vulnerabilities. Once the web application is secured, two deliberate backdoors are introduced: one that allows user-level access and another that provides root-level access. This setup prepares for the second phase, where servers will be exchanged with a partner group to test defenses and exploit each other’s vulnerabilities. All configurations are conducted on an ITU-hosted virtual machine accessed via SSH.

## 2. Initial Setup

### Server System Access

- SSH connection details
  - Student:
    - `ssh -J pensim@130.226.143.130 student@10.0.1.060`
    - `InjectBen10`
  
### Flask Installation
  
- Commands: `sudo apt update && sudo apt install python3-flask`
- Configuration details

## 3. System Hardening

### Found Vulnerabilities

- Login Bypassing with `' OR '1'='1` used as password.
- Import notes with unknown noteId with `1 OR 1=1` used as input in the Import External Notes input field.
- Direct connection between user input and database queries.
- A user having the same password as an unregistered user would trigger a "Password already in use" error message, upon registration.

### Web Application Security
  
  - Patching SQL Injection vulnerabilities
    - Through parameterized queries
    - Input sanitation

  - Techniques used (e.g., parameterized queries, input validation)

### Server Hardening
  - Added root password for root access and sudo commands, to prevent student users from getting root access using `sudo su`.
  - Found the following information about the server:
    
    - Linux Kernel: Debian 6.1.112-1
    - Services Running:
      - OpenSSH 9.2p1
      - Apache httpd 2.4.62 ((Debian))
    - The servers postgresql db is exposed on port 5432

### Testing and Verification
- We tested the SQL injection vulnerability using the same injections that previously worked. We found that the vulnerability was patched, as the injections no longer worked.
- We found an exploit for an Apache service that is running on our system, which we tested using metasploit. Our server was not exploitable, as the exploit seems to have been patched in our version of Apache.

## 4. System Services

- **Installed Services**:
  - Description of additional services added (e.g., FTP server, proxy server)
  - Purpose and configuration details
- **Service Versions**:
  - List of services with version numbers
  - Rationale for chosen services

## 5. Introduced Vulnerabilities

- **User Access Vulnerability**:
  
  - Description: How an attacker can gain shell access
  - Example methods: SQL injection, outdated service, hidden admin panel
  - Intended exploitation path
- **Root Access Vulnerability**:
  
  - Description: How an attacker can escalate to root
  - Example methods: Misconfigured CRON jobs, leaked credentials, SUID binaries
  - Detailed explanation of the intended escalation process

## 7. Conclusion

- **Summary**: Overview of system setup and defense mechanisms
- **Next Steps**: Brief on the upcoming attack phase and expected challenges

## Appendix

- **Useful Commands and Links**:
  - VPN setup link: [ITU Remote Access](https://itustudent.itu.dk/campus-life/it-services/remote-access)
  - List of commonly used passwords: [Top 1000 Passwords](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/10-million-password-list-top-1000.txt)

