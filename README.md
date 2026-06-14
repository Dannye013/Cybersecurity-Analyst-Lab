# Cybersecurity Analyst Lab

Hands-on security monitoring, log analysis, and network forensics labs built inside a dedicated virtual environment.

---

## Lab 01: Authentication Log Analysis & Digital Forensics
* Objective: Audit system authentication logs to verify authorized access paths and isolate connection indicators of compromise (IoCs).
* Operating System: Kali GNU/Linux
* Tools Used: Systemd journal logging infrastructure (`journalctl`)

### Investigation Summary
An authentication audit was conducted on the local node to track active remote access configurations and extract connection parameters. Using targeted system filtering tools, the direct session access footprint was isolated.

### Execution & Commands
To bypass standard noise and target specific OpenSSH server daemons, the following forensic filter command was executed in the administrative terminal:

```bash
sudo journalctl -u ssh -n 15
```
## Collected Evidence Data
Jun 08 10:08:20 Dannye013 systemd[1]: Starting ssh.service - OpenBSD Secure Shell server...
Jun 08 10:08:20 Dannye013 sshd[671]: Server listening on 0.0.0.0 port 22.
Jun 08 10:11:00 Dannye013 sshd-session[2312]: Accepted password for oluwaferanmi from 10.75.44.109 port 49744 ssh2
Jun 08 10:11:00 Dannye013 sshd-session[2312]: pam_unix(sshd:session): session opened for user oluwaferanmi(uid=1000)

## Forensic Analysis Breakdown
Target Account: oluwaferanmi (UID: 1000)

Source Address: 10.75.44.109 (Established network peer)

Target Service Gate: Port 22 (Standard SSH Service)

Authentication Method: Password Verification

Timestamp of Entry: Jun 08 10:11:00

Conclusion: The log file displays a normal, authorized cryptographic handshake. No brute force vectors, credential stuffing signatures, or unauthorized privileges were detected during this specific runtime window.

---

## Lab 02: Simulating and Detecting a Brute-Force Attack
* Objective: Simulate a malicious authentication attack, isolate Indicators of Compromise (IoCs), and analyze authentication failure signatures.
* Operating System:** Kali GNU/Linux
* Tools Used: Windows PowerShell (Attacker Node), `journalctl` (Forensic Engine)

### Threat Simulation Summary
A simulated threat actor targeted the system via an SSH brute-force vector, attempting to gain access by guessing administrative credentials. The attack was successfully terminated by the host architecture after multiple failures.

### Execution & Detection Commands
The attack pattern was generated using an external terminal node targeting the host network interface. To extract the malicious fingerprint, the central log engine was filtered:

```bash
sudo journalctl -u ssh -n 15

```

Jun 13 21:40:42 Dannye013 sshd-session[2530]: Invalid user admin from 192.168.0.81
Jun 13 21:40:59 Dannye013 sshd-session[2530]: Failed password for invalid user admin
Jun 13 21:41:07 Dannye013 sshd-session[2530]: Failed password for invalid user admin
Jun 13 21:41:16 Dannye013 sshd-session[2530]: Failed password for invalid user admin
Jun 13 21:41:17 Dannye013 sshd-session[2530]: Connection reset by invalid user admin
Jun 13 21:41:17 Dannye013 sshd-session[2530]: PAM 2 more authentication failures

Forensic Analysis Breakdown
Target Account Attempted: admin (Non-existent/Invalid system entity)

Attack Footprint: Rapid sequential authentications spaced within seconds (High-volume automation signature)

Result: Connection dropped via system authentication parameters (PAM) after 3 failed attempts.
