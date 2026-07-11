# Day 9 — Wireshark: TCP Analysis & Identifying Suspicious Patterns

**Tool:** Wireshark
**Platform:** Kali Linux
**Date Completed:** July 12, 2026

---

# Objective

Continue building on Day 8's Wireshark fundamentals by applying the `tcp` filter to view all TCP traffic, identify a complete TCP three-way handshake in a live capture, and practice recognizing patterns (like RST packets) that could indicate suspicious activity versus normal application behavior.

---

# Skills Practiced

* Starting a live packet capture and generating real browser traffic to analyze
* Applying the `tcp` display filter to isolate all TCP traffic
* Identifying a complete TCP three-way handshake (SYN → SYN,ACK → ACK) in real captured packets
* Recognizing TCP RST (reset) packets and understanding what they represent
* Distinguishing between normal application behavior and genuinely suspicious traffic patterns
* Reading Wireshark's built-in color-coding (e.g., red for resets) as an analysis aid

---

# Commands / Filters Practiced

```text
# Show all TCP traffic
tcp

# (Discussed, not yet applied) Isolate only reset packets
tcp.flags.reset == 1

# (Discussed, not yet applied) Isolate flagged retransmissions
tcp.analysis.retransmission
```

---

# Process 

I started a fresh Wireshark capture on `eth0` and generated traffic by browsing to Google and NeverSSL in Firefox. I ran into two small technical issues along the way: the Wireshark window didn't come to focus automatically after starting the capture (I had to click its icon in the taskbar to bring it forward), and clipboard copy-paste didn't work between my host machine and the Kali VM, so I had to type URLs directly instead of pasting them.

Once traffic was flowing, I applied the `tcp` filter, which showed roughly half of all captured packets were TCP (1849 out of 3714 total). Along the way, one of my first attempts to browse to a site failed with a "domain does not exist" error — I worked around this by trying a different, known-working site (NeverSSL) instead.

Looking through the results, I found a clean example of the three-way handshake I'd studied: two separate connections (source ports 44968 and 44978) both reaching the same destination IP on port 443 (HTTPS), each showing SYN, then SYN/ACK, then ACK in sequence — exactly matching the theory.

Further down the capture, I noticed several packets highlighted in red — these were RST (reset) packets on both of those same connections. I wasn't sure at first whether this indicated something suspicious, since it looked different from the graceful FIN/ACK teardown I'd studied. After discussing it, I learned that this was actually normal browser behavior — closing multiple parallel HTTPS connections it no longer needed, not a sign of a problem. This helped me understand what would actually be suspicious instead: resets coming from many different ports on one external IP (a sign of port scanning), or resets on a connection to a service that should normally stay open.

---

# Key Takeaways

* **A real three-way handshake looks exactly like the theory** — seeing SYN → SYN,ACK → ACK in an actual capture made the concept concrete rather than abstract.
* **Not every RST is suspicious.** Browsers routinely open multiple parallel connections and reset the ones they no longer need. Context matters more than the presence of a reset packet alone.
* **Wireshark's color-coding is a helpful starting point, not a verdict.** Red highlighting draws attention to resets, but it's up to the analyst to reason about whether that's normal or a genuine concern.
* **What would actually be suspicious:** repeated resets from a single external IP across many ports (port scan behavior), or resets on a connection to a service that should be reliably open — neither of which appeared in this capture.
* **Small environment issues are part of the job.** Window focus and clipboard sharing between host and VM are minor technical hiccups, not blockers — worth troubleshooting quickly and moving on.

---

# Challenges Faced

* The Wireshark window didn't automatically come to the foreground after starting the capture from the terminal; resolved by clicking its icon directly in the taskbar.
* Clipboard copy-paste didn't work between my Windows host and the Kali VM, so pasting a URL into Firefox failed. Resolved by typing the URL directly instead of pasting, since enabling bidirectional clipboard sharing in VirtualBox settings wasn't tested yet.
* Initially wasn't sure whether the red-highlighted RST packets indicated a problem; resolved by discussing the source/destination port pattern and learning that it reflected normal browser behavior rather than assuming red automatically means malicious.

---

# Connection to Real SOC Work

Recognizing normal versus suspicious TCP behavior is a core part of triaging alerts in a SOC environment. Automated tools will often flag anomalies like resets or retransmissions, but a huge part of an analyst's job is applying context to decide whether that's actually a problem. In this case, resets tied to normal browser connection management would not warrant escalation — but the same RST pattern appearing across dozens of ports from one external source, or on a connection to a critical internal service, would be a very different story and worth investigating further. This is exactly the kind of judgment call that separates alert fatigue (chasing every flagged packet) from effective triage (knowing what's actually worth escalating).

---

# Screenshots

The following screenshots will be added to the GitHub repository:

* Live capture running with `tcp` filter applied
* Clean three-way handshake (SYN → SYN,ACK → ACK) on two parallel connections
* RST packets highlighted in red, shown alongside the connections they belonged to
