# Day 6 — Linux Fundamentals Part 2 (Hands-on Kali Linux Practice)

**Platform:** Kali Linux 2026.1 (VirtualBox)  
**Date:** July 4, 2026  
**Status:** ✅ Completed

---

## Objective

Practice Linux system administration concepts including file
permissions, user and group management, process monitoring,
and file operations using Kali Linux. Since Linux Fundamentals
Part 2 on TryHackMe is now a premium room, I completed
equivalent hands-on exercises directly in my virtual machine
to strengthen the Linux skills required in cybersecurity
and SOC environments.

---

## What I Did

I practiced Linux concepts that closely align with the topics
taught in Linux Fundamentals Part 2 by performing hands-on
exercises in my Kali Linux virtual machine. During this lab,
I explored file permissions, user and group management,
process monitoring, and file operations while learning how
these concepts are applied in real-world cybersecurity
investigations.

---

## Commands Practiced

### File Permissions

- `touch day6test.sh` — Created a new test file
- `ls -la` — Displayed detailed file information including permissions
- `chmod 700 day6test.sh` — Changed permissions so only the owner could access it
- `ls -la day6test.sh` — Verified permission changes were applied

### User and Group Management

- `whoami` — Displayed the currently logged-in user
- `id` — Displayed user ID, group ID, and all group memberships
- `cat /etc/passwd` — Viewed all user accounts on the system
- `cat /etc/group` — Viewed all groups on the system

### Process Management

- `ps` — Displayed processes in current terminal session
- `ps aux` — Listed all running processes across the system
- `top` — Opened live real-time process monitoring interface
- `kill --help` — Reviewed how to terminate processes

### File Operations

- `cp day6test.sh day6backup.sh` — Copied a file
- `mv day6backup.sh day6renamed.sh` — Renamed the copied file
- `rm day6renamed.sh` — Deleted the renamed file
- `find / -name "*.log" 2>/dev/null` — Searched for log files
- `grep -r "kali" /etc/passwd` — Searched for user inside system file

---

## What I Learned

### File Permissions

Every file in Linux has three permission sets — Owner,
Group, and Others. Each can have Read, Write, and Execute
access.

When I ran `chmod 700 day6test.sh` the permissions changed
from `-rw-rw-r--` to `-rwx------` meaning only the owner
has full access and everyone else has no access at all.

Proper file permissions are critical in cybersecurity because
overly permissive files can expose sensitive data or allow
unauthorized users to modify important files.

### User and Group Management

The file `/etc/passwd` contains every user account on the
system including system and service accounts. Running `id`
showed my user belongs to several groups including sudo,
wireshark, bluetooth, and video.

During security investigations, analysts examine user accounts
and group memberships to identify unauthorized users or
privilege escalation attempts.

### Process Management

`ps aux` displayed every running process with the user who
started it, CPU usage, memory usage, process ID, and the
command used to start it. `top` provided a live view of
system activity, making it easier to identify processes
consuming excessive resources.

These commands are frequently used during incident response
to detect suspicious or malicious processes.

### File Operations

I practiced copying, renaming, deleting, and searching for
files. Using `find / -name "*.log" 2>/dev/null` searched
the entire system for log files while hiding permission
denied messages, making the output much cleaner and easier
to read.

---

## Skills Gained

- Linux File Permissions
- Linux User and Group Management
- Linux Process Monitoring
- Linux File Management
- File Searching with find
- Text Searching with grep
- Linux Command-Line Navigation
- Basic Linux System Administration
- Security-Oriented Linux Investigation

---

## Key Takeaways

- File permissions determine who can read, modify, or execute
files — making them essential to Linux security
- `/etc/passwd` and `/etc/group` are important files for
understanding user accounts and group memberships
- `ps aux` and `top` are valuable tools for monitoring
system activity and identifying suspicious processes
- `find` and `grep` are powerful commands for locating files
and searching their contents efficiently
- Hands-on Linux practice strengthens practical skills
required for SOC analyst roles

---

## Challenges Encountered

The command `find / -name "*.log"` returned hundreds of
results along with numerous permission denied messages.
I learned that adding `2>/dev/null` redirects error messages
away, resulting in cleaner output. This technique is commonly
used by Linux administrators and security professionals.

---

## Connection to Real SOC Work

The commands practiced today are used daily by SOC analysts:

- Reviewing file permissions to identify misconfigured files
- Examining `/etc/passwd` and `/etc/group` for unauthorized
user accounts or privilege escalation
- Monitoring active processes with `ps aux` and `top` to
detect suspicious activity
- Using `find` and `grep` to locate files and logs during
incident investigations

---

## Reflection

This lab reinforced the importance of understanding Linux
beyond basic navigation. File permissions, user accounts,
process monitoring, and file management are fundamental
concepts that directly support cybersecurity operations.

Although I could not access the premium TryHackMe room,
completing equivalent hands-on exercises in Kali Linux
allowed me to build practical experience and strengthen
my confidence with Linux administration. This knowledge
will be valuable in future SOC investigations and incident
response scenarios.

---

## Screenshots

Screenshots taken during this session showing:
- File permission changes before and after chmod 700
- User and group information from /etc/passwd and /etc/group
- Running processes from ps, ps aux, and top
- Results of file management and Linux command practice

---

*Completed as part of my 30-Day Cybersecurity Portfolio
Project.*
