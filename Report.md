# System Report


## Table of Contents

1. [Introduction](#1-introduction)
2. [Initial System Setup](#2-initial-setup)
3. [System Hardening](#3-system-hardening)
4. [System Services](#4-additional-services)
5. [Introduced Vulnerabilities](#5-introduced-vulnerabilities)
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

* We gathered the following information about the server, to find out what services are running on the server:

  - Debian 6.1.112-1
  - OpenSSH 9.2p1
  - Apache httpd 2.4.62 ((Debian))
  - PostgreSQL

  Using Metasploit we then checked to see if any of the services had known exploits. We found that the Apache service was vulnerable to an exploit, although in a previous version. We tested the exploit 
nonetheless, but the service was not exploitable.

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
  - OpenSSH 9.2p1
  - Apache httpd 2.4.62 ((Debian))
  - PostgreSQL

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
  
The password for the root account is very insecurely stored in a file /etc/.passwords.
The root user has full permissions for this file, but nobody else can read from, write to, or execute the file.

There is also a file called *runveryimportantscript*, which has the setuid permission set, allowing anyone to run it as root.
This file will run the script located at /tmp/scripts/.veryimportantscript.sh.
The root user has full permissions for the .veryimportantscript.sh file, while the group "student" can read and write.
An attacker can edit this script to cat the /etc/.passwords file into a file they can actually read from, such as /home/student/rootpwd.txt.
A clever attacker might even use a more direct attack, now that they can run any script as root.

Once the script has been edited, the attacker can simply run *runveryimportantscript*, which will run the script.
If the attacker obtains the root password this way, they will be able to use the sudo su command, or simply login as root via ssh.

#### Example usage 1 (The indented/handheld way):

This is assuming the attacker has student access, and have found the relevant files named above.

1. Go to the folder directory containing the script: `cd /tmp/scripts/`
2. Open the file with an editor: `nano veryimportantscript.sh`
3. Edit the script to contain the following: `cat /etc/.passwords > /home/student/rootpwd.txt`
4. run the "runveryimportantscript" file: `./runveryimportantscript`
5. Read the root password from the file: `cat /home/student/rootpwd.txt`
6. Use the password to sudo su or ssh as root.

#### Example usage 2 (The direct way):

1. Go to the folder directory containing the script: `cd /tmp/scripts/`
2. Open the file with an editor: `nano veryimportantscript.sh`
3. Edit the script to contain the following: `bash`


## Appendix

### Useful Information

* **SSH Connection**: `ssh -J pensim@130.226.143.130 student@10.0.1.060`
* **SSH Password**: InjectBen10
* **Root Password**: DinoNuggies123
  

