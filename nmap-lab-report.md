# Day 10–11 — Nmap Lab Report: Comparing Two Real Scans

**Tool:** Nmap  
**Platform:** Kali Linux  
**Date Completed:** July 17, 2026

---

# Objective

This is a retrospective analysis rather than a new hands-on session. I compared the two Nmap scans I previously performed during Day 7 (Hack The Box: Meow) and Day 10 (Hack The Box: Fawn) to better understand what Nmap's `-sC -sV` output reveals, how to interpret the results, and how different services can expose different types of security findings during reconnaissance.

---

# Tool Used

**Tool:** Nmap

Nmap (Network Mapper) is an open-source network discovery and security auditing tool used to identify live hosts, open ports, running services, operating systems, and potential vulnerabilities. I used Nmap because reconnaissance is one of the first steps in both penetration testing and defensive security investigations. Understanding what services are exposed helps security analysts identify potential attack surfaces before an attacker does.

---

# Skills Practiced

- Running `nmap -sC -sV` against live targets and interpreting the full output
- Understanding the purpose of the `-sC` (default script scan) and `-sV` (version detection) flags
- Reading Nmap's "Service Info" line to identify the target operating system
- Recognizing when an Nmap NSE script (such as `ftp-anon`) reveals more than just an open port by identifying an actual security misconfiguration
- Comparing two different scans to understand how vulnerabilities are exposed through reconnaissance
- Interpreting scan results from a security analyst's perspective

---

# Commands Practiced

```bash
# Used against both targets
nmap -sC -sV <target-ip>
```

---

# Process (In My Own Words)

This report is based on two Nmap scans I had already completed during previous Hack The Box exercises rather than a new scan. I revisited both scan results to better understand what Nmap revealed and how different services produce different types of findings.

## Scan 1 — Meow (10.129.169.135)

```text
PORT   STATE SERVICE VERSION
23/tcp open  telnet  Linux telnetd
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

The Meow scan identified a single open service running on **port 23 (Telnet)**. The scan itself did not reveal the complete vulnerability, but it immediately highlighted the use of an outdated and unencrypted remote administration protocol. Although the vulnerability (root login without a password) was only confirmed after connecting manually via Telnet, Nmap successfully identified the service that required further investigation.

## Scan 2 — Fawn (10.129.198.92)

```text
PORT   STATE SERVICE VERSION
21/tcp open  ftp    vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--   1 0   0    32 Jun 04 2021 flag.txt
Service Info: OS: Unix
```

The Fawn scan demonstrated how powerful Nmap's default scripts can be. Using the `ftp-anon` NSE script, Nmap automatically detected that anonymous FTP login was enabled and even listed the file available on the server before I manually connected. In this case, the scan itself revealed both the vulnerable configuration and the exposed file, making further investigation much more straightforward.

## Comparing the Two Scans

The most significant difference between the two scans was how much information each one provided.

The Meow scan identified an insecure service that required manual verification to determine the actual weakness. In contrast, the Fawn scan identified both the vulnerable service and the specific security misconfiguration automatically through Nmap's scripting engine.

Both scans also identified the operating system through the **Service Info** section:

- Meow → Linux
- Fawn → Unix

This information provides useful context when deciding what security risks or default configurations might exist on the target.

The comparison showed me that the same Nmap command can produce very different levels of insight depending on the services running on the target system.

---

# What the Output Showed

Both scans identified:

- Open network ports
- Running services
- Service versions
- Operating system information
- Additional information provided by Nmap NSE scripts

The Meow scan primarily identified an exposed Telnet service requiring further manual investigation.

The Fawn scan went further by automatically identifying anonymous FTP access and exposing a file available to unauthenticated users through the `ftp-anon` script.

---

# Suspicious Patterns Identified

- Telnet exposed on port 23 (legacy, unencrypted remote access)
- Anonymous FTP authentication enabled
- Sensitive file visible through anonymous FTP access
- Service version information disclosed
- Only one exposed service on each host, making that service the primary investigation target

---

# Key Takeaways

- The same two Nmap options (`-sC -sV`) can provide very different levels of information depending on the service being scanned.
- Version detection (`-sV`) is valuable because knowing the exact software version allows security analysts to research known vulnerabilities.
- Nmap's default scripting engine can identify actual security misconfigurations instead of simply reporting open ports.
- A single Nmap scan can significantly reduce the scope of an investigation by identifying the most important exposed service.
- Comparing multiple scans side by side reveals patterns that are not always obvious when examining a single host.

---

# Challenges Faced

This report was created using scans I had already completed during earlier labs, so no additional troubleshooting was required.

The technical challenges encountered during the original exercises—including reconnecting to the VPN, handling duplicate OpenVPN configuration files, and resolving FTP connection issues—are documented in the Day 7 (Meow) and Day 10 (Fawn) write-ups.

One valuable lesson from this report was recognizing that documentation and analysis require a different mindset than performing the technical tasks themselves. Reviewing previous work helped reinforce what each scan actually revealed.

---

# Connection to Real SOC Work

Nmap is commonly used during vulnerability assessments, security audits, and incident response to identify exposed services across an environment.

A SOC analyst or vulnerability management team would use scan results like these to identify systems that require immediate attention. For example:

- Systems exposing Telnet would likely be flagged because Telnet transmits credentials in plaintext and is considered insecure.
- Systems allowing anonymous FTP access would be investigated for unauthorized file exposure and misconfigured permissions.

The `ftp-anon` script also demonstrates how automated scanning tools can identify specific security findings without requiring analysts to manually test every service. Enterprise vulnerability scanners such as Nessus, Qualys, and OpenVAS use similar techniques to automatically identify known weaknesses across large environments.

---

# Screenshots

The following screenshots are included in this GitHub repository:

- `day7-nmap-scan.png` — Meow scan showing the Telnet service on port 23
- `day10-nmap-scan-ftp-anon.png` — Fawn scan showing FTP service detection and anonymous login allowed
- HTB Machine Solved screenshots from both labs for reference

---

# Conclusion

This comparison reinforced that Nmap is much more than a port scanner. Depending on the services running on a host, it can identify operating systems, detect service versions, execute vulnerability detection scripts, and reveal security misconfigurations during the reconnaissance phase.

By comparing the Meow and Fawn machines, I gained a better understanding of how the same scan command can produce very different investigative outcomes. This exercise strengthened my ability to interpret scan results from both a penetration testing and SOC analyst perspective.
