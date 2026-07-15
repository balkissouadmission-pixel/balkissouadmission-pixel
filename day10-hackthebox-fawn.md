# Day 10 — Hack The Box: Fawn (Starting Point)

**Machine:** Fawn
**Difficulty:** Very Easy
**Operating System:** Unix
**Category:** Starting Point
**Date Completed:** July 15, 2026

---

# Objective

Reconnect to Hack The Box via OpenVPN, scan a new Starting Point machine (Fawn) with Nmap, identify the vulnerable service, gain access, retrieve the flag, and work through Fawn's guided task series to reinforce the reasoning behind each step — not just the commands themselves.

---

# Skills Practiced

* Reconnecting to HTB via OpenVPN after a VPN session had dropped
* Locating and using a newly downloaded `.ovpn` file with a duplicate filename
* Verifying VPN tunnel status and target reachability (`ip a`, `ping`)
* Scanning a target with `nmap -sC -sV` and interpreting script output, not just port state
* Recognizing an anonymous FTP login vulnerability directly from Nmap's `ftp-anon` script result
* Connecting to an FTP server, authenticating anonymously, listing and downloading a file
* Reading FTP server response codes (e.g., `230 Login successful`)
* Working through HTB's guided task format, which tests understanding of each step rather than just requiring the final flag

---

# Commands Practiced

```bash
# Reconnect to HTB (new .ovpn file, escaping parentheses in the filename)
sudo openvpn ~/Downloads/starting_points_us-starting-point-2-dhcp\(1\).ovpn

# Confirm VPN tunnel and connectivity
ip a
ping -c 4 10.129.198.92

# Scan the target
nmap -sC -sV 10.129.198.92

# Connect via FTP
ftp 10.129.198.92
# username: anonymous
# password: (blank)

# Inside the FTP session
ls
get flag.txt
bye

# Read the flag
cat flag.txt
```

---

# Process (In My Own Words)

I reconnected to HTB by picking a new Starting Point machine, Fawn, rather than repeating Meow. My VPN session from Day 7 had actually dropped at some point without me realizing — running `ip a` showed no `tun0` interface at all, and a ping to the target failed completely. This wasn't something I'd expected, since I hadn't closed that terminal myself; it likely disconnected when the VM was restarted at some point between sessions.

I went back to HTB's connection page and downloaded a fresh `.ovpn` file. Since I'd already downloaded a file for the same VPN server back on Day 7, the new download got saved with `(1)` appended to the filename, which meant I had to escape the parentheses with backslashes to run it in the terminal. Once connected, I confirmed the new tunnel IP and successfully pinged Fawn.

Running `nmap -sC -sV` against the target immediately surfaced the vulnerability — only port 21 (FTP) was open, and Nmap's own `ftp-anon` script had already detected that anonymous login was allowed, even listing the exact file (`flag.txt`) sitting on the server. This meant the reconnaissance step essentially pointed straight at the answer, similar to how Meow's single open Telnet port did.

Connecting via FTP took a couple of attempts — my first login try produced garbled input in the terminal (repeated `^[[B` characters, likely from an accidental key combination), and pressing Ctrl+C to recover fully exited the FTP session rather than just clearing the line. I had to restart the FTP connection cleanly and type the username more carefully the second time. After that, logging in as `anonymous` with a blank password worked immediately (`230 Login successful`), and I was able to list the directory, download `flag.txt`, and read it.

Fawn's format was different from Meow's in one important way: instead of a single flag submission box, it used a series of guided tasks that asked me to identify specific details step by step — what FTP stands for, which port it uses, the secure alternative protocol (SFTP), the exact service version, the OS type, FTP response codes, and specific FTP client commands. Some of these answers came directly from my own scan output and terminal history (the port number, service version, OS type, response code), while others — general protocol terminology like FTP and SFTP, and the exact FTP client help-menu syntax — I confirmed through discussion rather than independent research. One task, asking for the command to display the FTP client's help menu, took several incorrect attempts (`help`, `?`, `HELP`) before I used the built-in hint, which revealed the expected answer was `ftp -?` — a flag-based syntax I hadn't tried.

---

# Key Takeaways

* **Nmap's scripting engine often does more than just list open ports.** The `ftp-anon` script didn't just flag a misconfiguration — it directly listed the sensitive file sitting on the server, which meant the recon step alone revealed most of what I needed.
* **VPN sessions can silently drop between working sessions**, even without manually closing the terminal — checking for `tun0` with `ip a` before assuming connectivity is a good habit, not an optional step.
* **Garbled terminal input during interactive sessions (like FTP) is worth restarting cleanly rather than trying to fix in place** — pressing Ctrl+C mid-input closed the whole session rather than just clearing text, which was a useful thing to learn firsthand.
* **Guided, task-based machines test understanding differently than a single flag submission.** Fawn required me to explain specific details (port numbers, response codes, command syntax) rather than just proving I got a shell — which is arguably a better test of whether I actually understood each step, not just whether I could follow a sequence of commands.
* **Not every answer comes from memory or reasoning alone.** The FTP help-menu syntax (`ftp -?`) wasn't something I guessed correctly — I needed the platform's own hint to get it, which is a normal and honest part of learning a new tool's specific conventions.

---

# Challenges Faced

* VPN session from Day 7 had disconnected without my noticing; resolved by downloading a fresh `.ovpn` file and reconnecting.
* The re-downloaded `.ovpn` file had a duplicate filename with `(1)` appended, requiring escaped parentheses in the terminal command.
* Garbled input during the first FTP login attempt, and pressing Ctrl+C fully exited the session rather than clearing the line; resolved by restarting the FTP connection cleanly.
* Several incorrect guesses (`help`, `?`, `HELP`) for the FTP client help-menu command before using HTB's built-in hint to find the correct syntax (`ftp -?`).

---

# Connection to Real SOC Work

Anonymous FTP access is a well-known, real-world finding in vulnerability assessments — many organizations unintentionally leave legacy FTP servers configured to allow anonymous read access, exposing files that were never meant to be public. This exercise reinforced that a single Nmap scan with script scanning enabled (`-sC`) can surface not just an open port, but a concrete, actionable finding (anonymous access allowed, specific file exposed) without any further manual probing. In a real environment, this would be documented as a finding requiring immediate remediation: disabling anonymous FTP access, migrating to SFTP, and auditing what data may have already been exposed.

---

# Flag

Retrieved via FTP (`get flag.txt`) and confirmed via `cat flag.txt`.

**Status:** ✅ Machine Solved — confirmed via HTB's "You have solved Fawn!" screen
**XP Earned:** 150

---

# Screenshots

The following screenshots will be added to the GitHub repository:

* VPN reconnection and successful ping to Fawn
* Nmap scan showing open FTP port and anonymous login detection
* FTP login and flag retrieval
* HTB "Machine Solved" confirmation
