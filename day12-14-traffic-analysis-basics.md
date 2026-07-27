# Day 12-14 — TryHackMe: Traffic Analysis Basics / Network Traffic Basics

## Objective
Understand what network traffic analysis is, why it matters for security monitoring, and practice placing a network tap in the most efficient location in a network topology to capture and inspect suspicious traffic (HTTP and DNS).

## Skills Practiced
- Understanding the purpose and value of Network Traffic Analysis (NTA)
- Identifying the most efficient location to place a network tap in a given topology
- Reading and interpreting HTTP request/response packets
- Reading and interpreting DNS query traffic
- Recognizing IDS evasion techniques
- Recognizing indicators of TCP session hijacking

## Commands / Tools Practiced
- Packet inspection via an in-browser packet viewer (Wireshark-style breakdown: Link, Internet, Transport, Application layers)
- Filtering/isolating traffic by protocol (HTTP, DNS) to locate a specific flag
- Reading TCP header fields (Sequence Number, Flags, Ack) directly from a packet breakdown

## Process 

I began by learning what Network Traffic Analysis (NTA) is and why it is an important part of cybersecurity. Before starting the hands-on exercises, I explored how SOC analysts use network traffic analysis to monitor network activity, investigate suspicious communication, troubleshoot performance issues, reconstruct attacks during incident response, and validate security alerts instead of relying only on automated tools.

One thing that stood out to me was that network traffic can be analysed at every layer of the TCP/IP model. Looking at the Link, Internet, Transport, and Application layers together provides a much clearer picture of what is happening on the network than focusing on only one layer.

I also learned several important technical concepts. One was **packet fragmentation**, where attackers split packets into smaller fragments in an attempt to evade an Intrusion Detection System (IDS), especially if the IDS and the destination system do not reassemble the fragments in the same way. Another was the importance of **TCP Sequence Numbers**, which help maintain an active TCP session and can also be used to identify possible TCP session hijacking attempts when unexpected sequence numbers appear.

The room also introduced different network traffic sources and protocols. I learned that **endpoints** generate most of the traffic within a typical network. In an Active Directory environment, **SMB** commonly uses **Kerberos** for authentication through the Domain Controller, although other authentication methods such as NTLM can also be used depending on the environment. I also reinforced that **TLS (Transport Layer Security)** is the protocol responsible for encrypting HTTPS traffic.

For the practical exercise, I worked with a simulated enterprise network containing a web proxy (WP1), firewall (FW1), router, switches, servers, and multiple workstations. My task was to determine the most effective location to place a network tap so I could capture malicious traffic without knowing which workstation had been compromised.

### Scenario 1 – Malicious PowerShell Download (HTTP)

Before placing the network tap, I examined the network topology to identify which device all HTTP traffic would pass through. Since the compromised workstation could have been any of the client machines, placing the tap next to a single endpoint would not have guaranteed that I captured the malicious traffic. Based on the traffic flow, I chose **WP1 (Web Proxy)** because every outbound and inbound HTTP request passed through it. This turned out to be the correct placement because it allowed me to monitor all web traffic regardless of which workstation initiated the connection.

After placing the tap, I inspected both the HTTP request and the HTTP response packets using the packet viewer. I expanded the different protocol layers and focused on the Application layer to examine the HTTP information.

The request packet showed:

`GET www.example.com/downloads/update.zip`

This simply confirmed that the workstation was requesting a file from the web server.

The response packet contained much more useful information. I examined the response headers and noticed:

* `Content-Type: application/octet-stream`
* `Content-Disposition: attachment; filename="install.ps1"`
* `Content-Length: 10485760`

The filename **install.ps1** immediately stood out because PowerShell scripts are commonly abused to deliver malware. I then continued reviewing the response until I found the **Body Preview** section, where the flag `THM{FoundTheMalware}` was located.

This exercise reinforced an important lesson: the request only tells me what the client asked for, while the response provides evidence of what was actually delivered.

### Scenario 2 – DNS Tunnelling / Command and Control (C2)

The second exercise followed the same reasoning process, but this time I analysed DNS traffic. Since every workstation sends DNS queries to the DNS server, it also acts as a central point for monitoring DNS communication.

After reviewing the DNS response packet, I confirmed that it contained a **TXT record**. I then examined the returned data and found:

* Query: `c2.tryhackrne.thn`
* Response: `c2.tryhackrne.sthn`
* TXT Record Data: `THM{C2CommandFound}`

Seeing data returned through a TXT record immediately reminded me of **DNS tunnelling**, where attackers abuse DNS traffic to transfer commands or other information between a compromised system and a command-and-control (C2) server. Because DNS traffic is often trusted and may receive less inspection than other protocols in some environments, it can become an effective channel for attackers if organisations do not monitor it properly.

Overall, this room helped me understand that effective network traffic analysis is not just about reading packets. It also requires understanding how traffic flows through a network, placing monitoring devices in the right location, examining packet details carefully, and using the information collected to identify suspicious activity and validate potential security incidents.

## Key Takeaways
- Tap/sensor placement matters as much as the analysis itself — the "best" location is wherever all relevant traffic is guaranteed to pass through (proxy, DNS server, or router), not wherever is closest to a suspected source.
- Requests and responses are two separate packets/directions — the interesting data (a payload, a flag, a malicious script) is almost always in the **response** (headers + body), not the request.
- DNS TXT records can be abused to smuggle data in or out of a network (DNS tunneling) — a reminder that DNS traffic deserves the same scrutiny as HTTP, not less.
- IDS evasion and session hijacking detection both come down to understanding normal TCP/HTTP/DNS behavior well enough to spot what's abnormal.

## Challenges Faced
This one took longer than expected. I had the tap placement logic down quickly, but got stuck specifically on finding the flag inside the HTTP response packet — I was looking at the request fields and the summary metadata, not realizing the flag was sitting in a separate "Body Preview" field further down in the response packet. Once I understood which field held the actual body content (vs. headers vs. request info), it clicked. Stepping away and coming back with a clearer head made the difference.

## Connection to Real SOC Work
This mirrors real SOC/network analyst work directly: knowing where to place sensors or taps in a real network (at proxies, firewalls, or DNS servers) is foundational to actually being able to see an attack at all. It also reinforces validating alerts by looking at the raw traffic yourself rather than trusting a summary — which is exactly what a SOC analyst does when triaging an alert tied to a phishing download or suspicious DNS activity.

## Screenshots
*(Insert: room overview, topology with tap placed on WP1, expanded HTTP request packet, expanded HTTP response packet with flag, room completion screen)*
