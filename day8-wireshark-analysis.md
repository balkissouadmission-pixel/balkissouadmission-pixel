# Day 8 — Wireshark Basics: Capturing and Analyzing Live Traffic

**Tool:** Wireshark
**Platform:** Kali Linux
**Date Completed:** July 9, 2026

---

# Objective

Capture live network traffic on my Kali Linux VM using Wireshark, apply display filters to isolate specific types of traffic (HTTP, IP-based), and identify what unencrypted traffic reveals versus encrypted traffic.

---

# Skills Practiced

* Starting and stopping a live packet capture
* Applying Wireshark display filters (`http`, `ip.addr`, `ip.src`)
* Reading and interpreting the TCP three-way handshake (SYN, SYN/ACK, ACK)
* Identifying TCP connection teardown (FIN, ACK)
* Expanding protocol layers within a single packet (Ethernet → IP → TCP → HTTP)
* Reading raw HTTP request headers in plaintext
* Distinguishing encrypted (TLS) traffic from unencrypted (HTTP) traffic
* Understanding the difference between bidirectional and unidirectional IP filters

---

# Commands / Filters Practiced

```text
# Show only HTTP (unencrypted) traffic
http

# Show only TLS (encrypted) traffic
tls

# Show all traffic to AND from a specific IP address
ip.addr == 172.217.20.195

# Show traffic FROM a specific IP address only
ip.src == 172.217.20.195
```

---

# Process 

I opened Wireshark inside Kali and started a live capture on the `eth0` interface. As soon as I started, I immediately saw background traffic (ICMPv6 router/multicast messages) that my machine sends automatically — this wasn't traffic I generated, just normal network chatter.

To generate traffic I could actually analyze, I opened Firefox and browsed a couple of sites, including `neverssl.com`, a site built specifically to use plain, unencrypted HTTP instead of HTTPS.

I first applied the `tls` filter to see encrypted traffic, which showed "Application Data" — Wireshark can see that a secure conversation is happening, but not the actual content, since it's encrypted.

I then applied the `http` filter to see unencrypted traffic instead. This showed a mix of real traffic, including `GET /success.txt` requests and `200 OK` responses from the NeverSSL page loading, alongside other background HTTP traffic I hadn't specifically generated myself.

Out of curiosity, I clicked into one of the HTTP packets shown and expanded the "Hypertext Transfer Protocol" layer to see it broken down field by field. This particular packet turned out to be a separate background request — a `POST /wr2` request to `o.pki.goog` (Google's certificate-checking service), not the NeverSSL page itself. Even so, it made the same point clearly: the entire request was fully readable in plain text, including my browser's exact User-Agent string (Firefox version, operating system), Accept-Language, Accept-Encoding, and more — all visible without any decryption. This was the clearest demonstration of why unencrypted protocols are a security risk, regardless of which specific request it came from: anyone capturing this traffic on the same network could read it directly.

Next, I practiced filtering by IP address. I used `ip.addr == 172.217.20.195` (a Google IP that showed up repeatedly) and saw the full conversation in both directions: the TCP handshake (SYN → SYN,ACK → ACK), the request/response exchange, TCP Keep-Alive messages keeping the connection open for reuse, and finally the connection closing (FIN, ACK).

To understand the difference between directional filters, I then changed the filter to `ip.src == 172.217.20.195`. This immediately cut the number of displayed packets roughly in half — from 57 packets down to 27 — because it now only showed traffic originating from that IP, not traffic going to it. This made the distinction between `ip.addr` (both directions) and `ip.src`/`ip.dst` (one direction only) very clear in practice, not just in theory.

---

# Key Takeaways

* **HTTP is unencrypted by design.** Every field of a request — including the exact browser and OS in use — is fully readable to anyone capturing the traffic. This is the core reason HTTPS/TLS exists.
* **TLS hides content, not existence.** With the `tls` filter, I could see that a secure conversation was happening, but not what was being said. Encryption protects the payload, not the fact that communication occurred.
* **A single IP filter tells a complete story.** Using `ip.addr`, I could trace an entire conversation from connection setup to teardown — this is exactly the kind of "follow one host's activity" investigation a SOC analyst does when looking into a suspicious connection.
* **Directional filters matter.** `ip.addr` shows both directions of traffic; `ip.src` (or `ip.dst`) isolates just one side. Knowing which to use depends on whether you're trying to see a full conversation or isolate what one specific host is sending or receiving.
* **Background noise is normal.** Not every captured packet is meaningful — protocols like ICMPv6 and OCSP appear constantly as part of normal system and browser behavior, and part of analysis is learning to filter that out.

---

# Challenges Faced

* Typed filter terms (like "http") into Google's search bar instead of Wireshark's own filter bar on two separate occasions — an easy mix-up when multiple windows are open side by side. Resolved both times by explicitly switching focus to the Wireshark window before typing the filter.
* Most modern web traffic uses QUIC or TLS instead of plain HTTP, so an initial `http` filter on a regular HTTPS site returned very little. Used `neverssl.com`, a site deliberately built without encryption, to generate genuine HTTP traffic to analyze.
* Misidentified one packet as loading the NeverSSL page when it was actually an OCSP certificate-check request — a reminder to check the actual protocol and request details rather than assuming based on timing alone.

---

# Connection to Real SOC Work

Packet capture and filtering are core SOC analyst skills used during incident investigations. If a host on a network is flagged for suspicious behavior, a SOC analyst would use exactly this kind of `ip.addr` filtering to reconstruct that host's full conversation with a suspicious external IP — identifying what was sent, what was received, and whether any of it was in cleartext (and therefore may have exposed sensitive data).

Seeing legitimate HTTP traffic in plaintext also reinforces why security teams flag any application or internal service still using HTTP instead of HTTPS as a finding requiring remediation — the same exposure demonstrated here with browser headers could just as easily expose session tokens, credentials, or other sensitive data on a real, unencrypted internal service.

---

# Screenshots

The following screenshots will be added to the GitHub repository:

* Live capture running on `eth0`
* `http` filter showing GET/200 OK requests
* Expanded HTTP packet showing raw, readable request headers
* `ip.addr` filter showing full two-way conversation with one IP
* `ip.src` filter showing the reduced, one-directional result for comparison
