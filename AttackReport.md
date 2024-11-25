# Attack Report

*Your review report must be at most 5 pages and must describe (a) the steps you've taken and (b) your results for each of the following headings:Deadline: 26 November at 23:59 (the day before the workshop)*

## Table of Contents

1. [Information gathering](#1-Information-gathering)
2. [Initial access](#2-initial-access)
3. [Privilege escalation](#3-privilege-escalation)
4. [Security goal violations](#4-security-goal-violations)
5. [Maintaining access](#5-maintaining-access)

## 1. Information Gathering

The only information we gathered, or was given, was the IP and username of their server. Now that we had a user, which was named 'Weakuser', our first thought was to brute force the password for it. As a constraint for the Defense part of the assignment, brute force vulnerable passwords must be on the top 1000 list of passwords handed out to us.

## 2. Initial Access

To brute force the password we used Hydra on a Kali Linux VM. It took 5 minutes to brute force the password using the .txt file containing the top 1000 passwords, and the result was the following:

`12qwaszx`

## 3. Privilege Escalation

Now that we could log in to the server, we simply tried to escalate privilage using `sudo su`, which required the same password as for the user we had just hacked. This attempt therefore was successful, and we now had root access.

## 4. Security Goal Violations

**Confidentiality** was violated as we as an adversary could access everything on the server using the root privilege.

**Availability** was compromised since we attained control of the server with root access, and therefore had the power to take down the application.

**Integrity** was undermined as we had control of the server with root access, allowing us to modify data contained in the server.

## 5. Maintaining Access

