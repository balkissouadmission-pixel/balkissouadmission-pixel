# Day 9 — Wireshark: TCP Analysis & Identifying Suspicious Patterns

**Tool:** Wireshark
**Platform:** Kali Linux (VirtualBox)
**Date Completed:** July 12, 2026

---

# Objective

Continue building on Day 8's Wireshark fundamentals by applying the `tcp` display filter to view all TCP traffic, identify a complete TCP three-way handshake in a live packet capture, and practice recognizing patterns, such as TCP RST (reset) packets, that could indicate suspicious activity versus normal application behavior.

---

# Skills Practiced

* Starting a live packet capture and generating real browser traffic for analysis
* Applying the `tcp` display filter to isolate TCP traffic
* Identifying a complete TCP three-way handshake (SYN → SYN,ACK → ACK) in captured packets
* Recognizing TCP RST (reset) packets and understanding what they represent
* Distinguishing between normal application behavior and genuinely suspicious traffic patterns
* Using Wireshark's built-in color-coding as an aid during packet analysis

---

# Commands / Filters Practiced

```text
# Show all TCP traffic
tcp

# Learned during this lab (not applied in this capture)
tcp.flags.reset == 1

# Learned during this lab (not applied in this capture)
tcp.analysis.retransmission
```

---

# Process (In My Own Words)

I started a new Wireshark capture on the `eth0` network interface and generated traffic by browsing to Google and NeverSSL using Firefox.

During the lab, I encountered two minor technical issues. First, after launching Wireshark from the terminal, the application window did not automatically come to the foreground, so I clicked the Wireshark icon in the taskbar to bring it into focus. Second, clipboard copy-and-paste was not working between my Windows host machine and the Kali Linux virtual machine, so I manually typed the URLs into Firefox instead.

Once traffic was flowing, I applied the `tcp` display filter, which showed that approximately half of the captured packets were TCP traffic (1,849 out of 3,714 total packets). One of my initial attempts to browse to a website returned a "domain does not exist" error, so I switched to NeverSSL, which successfully generated additional traffic for analysis.

While reviewing the capture, I found a clear example of the TCP three-way handshake. Two separate connections (source ports 44968 and 44978) both connected to the same destination IP address on port 443 (HTTPS). Each connection followed the expected sequence of SYN → SYN,ACK → ACK, exactly matching what I had learned about the TCP three-way handshake.

Later in the capture, I noticed several packets highlighted in red. These were TCP RST (reset) packets associated with the same HTTPS connections. At first, I wasn't sure whether these packets indicated suspicious activity because they looked different from the graceful FIN/ACK connection termination I had previously studied. After investigating further, I learned that these resets represented normal browser behavior. Modern web browsers often open multiple parallel HTTPS connections and terminate the ones they no longer need using RST packets. This helped me understand that context is critical when analyzing network traffic.

I also learned what would actually be considered suspicious. For example, numerous RST packets coming from a single external IP address across many different destination ports could indicate port scanning activity. Likewise, unexpected resets on a long-lived connection to a critical internal service could warrant further investigation.

---

# Key Takeaways

* **A real TCP three-way handshake looks exactly like the theory.** Observing SYN → SYN,ACK → ACK in a live packet capture reinforced my understanding by showing the process in a real network environment.
* **Not every TCP RST packet is suspicious.** Browsers routinely open multiple HTTPS connections and reset those they no longer need. The presence of a reset packet alone does not indicate malicious activity.
* **Wireshark's color-coding is a helpful starting point, not a conclusion.** Red highlighting draws attention to packets that deserve investigation, but analysts must rely on context before determining whether activity is suspicious.
* **Context is essential during analysis.** Repeated resets across many destination ports from the same external IP could indicate scanning behavior, while resets on normal browser sessions are expected.
* **Troubleshooting is part of cybersecurity.** Small environment issues, such as application focus or clipboard sharing between host and virtual machines, are common and should be resolved efficiently without interrupting the investigation.

---

# Challenges Faced

* The Wireshark window did not automatically come to the foreground after launching it from the terminal. I resolved this by selecting the application from the taskbar.
* Clipboard copy-and-paste between my Windows host and Kali Linux virtual machine was unavailable, so I manually entered the URLs into Firefox instead.
* Initially, I was unsure whether the red-highlighted TCP RST packets indicated suspicious activity. By examining the source and destination ports and understanding browser behavior, I concluded that the resets were normal rather than malicious.

---

# Connection to Real SOC Work

Recognizing the difference between normal and suspicious TCP behavior is an important skill for SOC analysts. Security monitoring tools frequently generate alerts for events such as TCP resets or retransmissions, but an analyst must investigate the surrounding context before determining whether an alert requires escalation.

In this exercise, the TCP RST packets were associated with normal browser connection management and did not indicate malicious activity. However, similar reset patterns occurring across multiple ports from the same external IP address, or on long-lived connections to critical internal services, could indicate scanning, service disruption, or other malicious behavior requiring further investigation.

This lab reinforced the importance of analyzing network traffic carefully before reaching conclusions and demonstrated how understanding normal behavior helps reduce false positives while allowing analysts to focus on genuine security incidents.

---

# Screenshots

The following screenshots are included in the GitHub repository:

* `day9-tcp-filter-applied.png` — The `tcp` display filter applied, showing TCP traffic generated during the live capture.
* `day9-handshake-and-rst-packets.png` — A single packet capture showing both the TCP three-way handshake (SYN → SYN,ACK → ACK) and the TCP RST packets that appeared shortly afterward on the same HTTPS connections.
