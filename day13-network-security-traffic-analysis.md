# Day 13 — TryHackMe: Network Security and Traffic Analysis Essentials

## Objective

Understand the basic concepts of Network Security and Traffic Analysis, then apply those concepts in a hands-on simulation by identifying and filtering malicious IP addresses and IP/port combinations using a simulated IDS/IPS environment.

---

## Skills Practiced

* Understanding the different layers of Network Security and how they work together
* Distinguishing Access Control from Threat Control and identifying the technologies used for each
* Understanding Managed Security Services (MSS/MSSP) and why organisations use them
* Understanding the difference between Flow Analysis and Packet Analysis
* Correlating IDS/IPS alerts with network traffic to identify malicious activity
* Applying firewall filtering to block malicious IP addresses and suspicious ports in a simulated environment

---

## Commands / Tools Practiced

* No CLI commands were used during this room. The exercises were completed through an interactive browser-based simulation that included a Traffic Analyser, IDS/IPS system, and firewall filtering interface.

---

# Process 

Today I learned the foundations of Network Security and how authentication and authorisation work together to control access to systems and resources.

One of the first concepts I learned was **Access Control**. I learned that Access Control is the first layer of Network Security. Its purpose is to ensure that users and devices are properly authenticated and authorised before they are allowed to access network resources. Technologies that support Access Control include Firewalls, Network Access Control (NAC), and Identity and Access Management (IAM).

After access is granted, **Threat Control** helps monitor network activity for suspicious or malicious behaviour. This layer uses technologies such as IDS/IPS, Data Loss Prevention (DLP), Endpoint Protection, Cloud Security, and SIEM to detect, analyse, and respond to potential threats.

I also learned that **Traffic Analysis** is the process of intercepting, monitoring, recording, and analysing network communications. This helps security teams identify suspicious activity, investigate incidents, troubleshoot network problems, and improve overall network visibility.

For the hands-on simulation, I initially struggled with the Level 2 challenge because I wasn't sure which traffic should be filtered first. After reviewing the traffic more carefully, I decided to focus on unusual ports before looking at the IP addresses. Ports such as **4444**, **7777**, and **2222** stood out because they are not commonly used for standard network services. Filtering those suspicious ports helped me identify the malicious traffic and successfully retrieve the Level 2 flag.

### Flags Obtained

* **Level 1 (Malicious IP Filtering):** `THM{PACKET_MASTER}`
* **Level 2 (Malicious IP + Port Filtering):** `THM{DETECTION_MASTER}`

---

# Key Takeaways

* Authentication and authorisation are two foundational concepts of Network Security.
* Access Control and Threat Control work together to protect a network. Access Control determines who is allowed to access resources, while Threat Control monitors activity and detects malicious behaviour after access has been granted.
* Flow Analysis provides a summary of network communications, while Packet Analysis examines the actual packet contents for deeper investigation.
* Unusual ports can be useful indicators during an investigation, but they should always be analysed in context because not every uncommon port is malicious.
* Combining IP address filtering with port filtering provides a more precise way of identifying and isolating malicious traffic than filtering by IP address alone.

---

# Challenges Faced

During the hands-on simulation, I encountered an issue with the firewall filter input box. The field was not clearing between entries, so when I entered a second IP address it was appended to the first instead of being added separately. This created an invalid IP address and prevented the malicious traffic from being filtered correctly.

To resolve the issue, I restarted the simulation, manually cleared the input field before entering each IP address, and clicked **"Add To Filter"** after every entry instead of pressing **Enter**. Once both IP addresses were added correctly, the malicious traffic was successfully filtered and I was able to complete the challenge.

This experience reminded me that troubleshooting small interface issues is an important part of completing technical tasks successfully.

---

# Connection to Real SOC Work

This exercise gave me an introduction to how SOC analysts triage security alerts. An IDS/IPS system can generate many alerts, but not every alert represents a genuine security incident. Some events may simply indicate policy violations or unusual user activity, while others may point to real attacks that require immediate investigation.

By comparing the IDS/IPS alerts with the network traffic data, I practised deciding which traffic should be filtered and which events required attention. This demonstrated the importance of analysing evidence before taking action rather than relying solely on automated alerts.

---

# Screenshots

* Access Control vs Threat Control overview (concept table)
* Traffic simulation before filtering (topology showing malicious traffic flagged)
* Traffic simulation after filtering (topology showing traffic resolved)
* Room completion screen
