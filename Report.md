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

### Server Access

The first the step was to access the server using SSH. The following commands were used to connect to the server:

    ssh -J pensim@130.226.143.130 student@10.0.1.060

As the the server came with a default password, the first step was to change the password for the student user. The following command was used to change the password:

    passwd

The password was then changed to **InjectBen10**

### Flask Installation

The next step was to install Flask on the server, to be able to run the application. The following command was used:

    sudo apt update && sudo apt install python3-flask

## 3. System Hardening

### Found Vulnerabilities

* Input fields in the application were vulnerable to SQL injection. The following vulnerabilities were found:

  - **Login Bypassing**

    A user could bypass the login system by using the following SQL injection in the *Password Field*:

      `' OR '1'='1`

  - **Import Notes Without NoteId**

    Any authenticated user could import other users' notes without using the notes corresponding NoteId. This could be done with SQL Injection in the *Import External Notes* input field.

* The application was also vulnerable by not allowing users to register with the same password as any other user. Furthermore, the application showed the an error message saying "Password already in use" when a user tried to register with a password already in use. This vulnerability would let an adversary know what passwords are used by the application's users.

* We gathered the following information about the server, to find out if there are any services running that could be exploited.


### Application Hardening

* The SQL Injection vulnerability was patched by refactoring to parameterized queries. An example of the refactored code is shown below:

  ```python
  userid =  session['userid']
  statement = '"SELECT * FROM notes WHERE assocUser = :userid"'
  print(statement)
  c.execute("SELECT * FROM notes WHERE assocUser = ?", (userid,))
  ```

* The password uniqueness vulnerability was patched by removing the error message that showed when a user tried to register with a password already in use.


### Server Hardening

* The server originally allowed student users to run `sudo su` to gain root access. This was patched by adding a root password for root access and sudo commands. The following command was used to add the root password:

      sudo passwd root

  The root password was then set to **DinoNuggies123**.

* Using Metasploit, we found that the server was running an Apache service that was vulnerable to an exploit. The exploit was tested, and we found that the server was not exploitable. The vulnerability seems to have been patched in the version of Apache running on the server.

* All packages on the server were updated to the latest version using the following command:

      sudo apt update && sudo apt upgrade


## 4. System Services

- **Installed Services**:
  - Description of additional services added (e.g., FTP server, proxy server)
  - Purpose and configuration details
  - List of services with version numbers
  - Rationale for chosen services

- **Server Information For Later Use**:
    - Linux Kernel: Debian 6.1.112-1
    - Services Running:
      - OpenSSH 9.2p1
      - Apache httpd 2.4.62 ((Debian))
    - The servers postgresql db is exposed on port 5432

## 5. Introduced Vulnerabilities

### User Access Vulnerability (Remote code execution)

A remote code execution vulnerability has been introduced in the /notes endpoint, on the post method.
Before saving a note to the database, the note is ran as code, in case the user wants to filter the text with custom
python, or if the user just wants to run the python code in his/her note.

This enables the user to import system libraries and the likes, execution arbitrary code on the host machine.

Example usage:
 - Login
 - Create a new note
 - Type __import__('subprocess').check_output('ls', shell=True) in note input field
 - Post note.
 - See the result of the executed code in the errormessage.

### Root Access Vulnerability
  
When a has user-level access, they can escalate their privileges to root by exploiting the following vulnerability.

Inside the /tmp directory, a hidden folder named `Cronjobs` was created. Inside this folder, a script named `VeryImportantScript.sh` was created. This script is running with root privileges, but is made to be readable and writable by the student user. The script is running every minute, and the student user can modify the script to run any command as root. 

A student user can exploit this vulnerability by modifying the script to run a command that gives the student user root access. There are many ways to do this, but one example is to add the following line to the script:

```
Some code that gives the student user root access
```

## 7. Conclusion



## Appendix

### Useful Information

* **SSH Connection**: `ssh -J pensim@130.226.143.130 student@10.0.1.060`
* **SSH Password**: InjectBen10
* **Root Password**: DinoNuggies123
  

