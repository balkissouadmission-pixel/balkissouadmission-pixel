# Day 7 — Hack The Box: Meow (Starting Point)

**Machine:** Meow
**Difficulty:** Very Easy
**Operating System:** Linux (Ubuntu 20.04.2 LTS)
**Category:** Starting Point
**Date Completed:** July 7, 2026

---

# Objective

Connect my Kali Linux virtual machine to the Hack The Box network using OpenVPN (instead of relying on the Pwnbox, since my free trial had expired), scan the target machine, identify the vulnerable service, gain access, and retrieve the root flag.

---

# Skills Practiced

* OpenVPN configuration and VPN connectivity
* Linux command-line navigation
* Network troubleshooting
* Host connectivity testing with `ping`
* Network reconnaissance using Nmap
* Service enumeration
* Telnet authentication
* Basic post-exploitation
* File system navigation
* Root flag retrieval

---

# Commands Practiced

```bash
# Connect Kali Linux to the HTB VPN
sudo openvpn starting_points_us-starting-point-2-dhcp.ovpn

# Confirm the VPN tunnel is active
ip a

# Verify the target is reachable
ping -c 4 10.129.169.135

# Scan for open ports and services
nmap -sC -sV 10.129.169.135

# Connect to the Telnet service
telnet 10.129.169.135

# Locate and read the flag
ls -la /root
cat /root/flag.txt
```

---

# Process (In My Own Words)

I started by downloading the `.ovpn` connection file from the Hack The Box **Starting Point** section. My first attempt failed because I downloaded the file using my Windows browser instead of the browser inside my Kali virtual machine. Since Windows and Kali are separate operating systems running in VirtualBox, the file never appeared inside Kali.

After opening Firefox inside Kali, logging into Hack The Box, and downloading the file again, it was saved correctly in the `Downloads` directory.

I connected to the VPN using:

```bash
sudo openvpn <filename>.ovpn
```

To confirm the VPN tunnel was working, I checked for the `tun0` interface with:

```bash
ip a
```

While troubleshooting, I accidentally started multiple OpenVPN sessions by reconnecting several times. This created routing conflicts. I resolved the issue by terminating every OpenVPN process:

```bash
sudo pkill openvpn
```

After that, I reconnected with a single VPN session, and everything worked normally.

Next, I tested connectivity with `ping`, but initially received **Destination Host Unreachable**. After investigating, I realized the HTB machine had timed out and automatically shut down. I restarted the machine from the HTB dashboard, received a new target IP address, and the host became reachable immediately.

I then performed reconnaissance using:

```bash
nmap -sC -sV <target-ip>
```

The scan revealed only one open service:

* **23/tcp — Telnet**

This immediately suggested the intended vulnerability. Telnet is an outdated protocol that transmits data without encryption and should never be exposed in a production environment.

I connected using:

```bash
telnet <target-ip>
```

When prompted for a username, I entered:

```text
root
```

No password was requested, and I was immediately given a root shell:

```text
root@Meow:~#
```

To retrieve the flag, I first tried:

```bash
cat /root/root.txt
```

Since that file did not exist, I listed the contents of the directory:

```bash
ls -la /root
```

I located `flag.txt` and displayed its contents with:

```bash
cat /root/flag.txt
```

The flag was successfully submitted, and the machine was marked as solved.

---

# Key Takeaways

* **Telnet is a critical security risk.** It transmits credentials and session data in plaintext. In this lab, it even allowed a root login without requiring a password, demonstrating how dangerous insecure legacy services can be.
* **Reconnaissance always comes first.** A single `nmap -sC -sV` scan quickly identified the only exposed service and revealed the intended attack path.
* **Environment setup is part of cybersecurity.** Understanding the difference between the Windows host and the Kali virtual machine, as well as resolving VPN conflicts, are practical troubleshooting skills that are valuable in both lab environments and real IT operations.
* **Read error messages carefully.** The "No such file or directory" error simply meant I had guessed the wrong filename. Listing the directory revealed the correct file immediately.
* **Copy and paste sensitive values whenever possible.** My first flag submission failed because I manually typed the hexadecimal string from a screenshot. Copying it directly from the terminal eliminated transcription errors—a good habit when working with hashes, API keys, credentials, or other sensitive data.

---

# Challenges Faced

* Downloaded the `.ovpn` file into the Windows host instead of the Kali virtual machine and had to download it again from inside Kali.
* Accidentally created multiple OpenVPN sessions, resulting in routing conflicts. Solved by terminating all OpenVPN processes and reconnecting with a single session.
* Initial connectivity failed because the HTB machine had timed out. Restarting the machine generated a new IP address and restored connectivity.

---

# Connection to Real SOC Work

This lab demonstrates a vulnerability that security teams still encounter in real environments. Legacy services such as Telnet, FTP, or SNMP configured with weak or default authentication continue to appear during vulnerability assessments.

A SOC analyst or vulnerability management team performing network scans would classify an exposed Telnet service—especially one allowing root access without a password—as a **Critical** finding.

Recommended remediation would include:

* Disabling the Telnet service completely.
* Replacing Telnet with SSH for encrypted remote administration.
* Disabling direct root logins.
* Enforcing strong authentication and least-privilege access.
* Restricting remote management access through firewall rules or VPN.
* Reviewing system logs to determine whether unauthorized access has already occurred.

---

# Flag

Successfully retrieved using:

```bash
cat /root/flag.txt
```

Submitted successfully on Hack The Box.

**Status:** ✅ Machine Solved

**XP Earned:** 150

**Achievement:** Reached Level 2

> **Note:** My first flag submission failed because I manually typed the 32-character hexadecimal string from a screenshot and made a small transcription error. Copying the flag directly from the terminal resolved the issue. This reinforced the importance of copying sensitive values instead of retyping them manually.

---

# Screenshots

The following screenshots will be added to the GitHub repository:

* VPN connection established (`tun0`)
* Successful Nmap scan
* Telnet login as root
* `flag.txt` retrieval
* HTB "Machine Solved" confirmation
