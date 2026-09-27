# 🦈 Wireshark Network Analysis Lab 
Watch Me live here:(https://www.loom.com/share/79e7c9162aed4856958d349af046d272)

## Overview

This lab demonstrates practical network traffic analysis using Wireshark. Through five hands-on exercises, we explore how data moves across a network — from DNS resolution to unencrypted HTTP traffic — and highlight real-world cybersecurity implications including why protocol security matters.

**Tools Used:** Wireshark, Windows Command Prompt  
**Skill Level:** Beginner to Intermediate  
**Purpose:** Network traffic analysis, DNS inspection, HTTP security awareness

---

## 🔧 Prerequisites

- Wireshark installed ([download here](https://www.wireshark.org/download.html))
- Windows Command Prompt
- Active internet connection
- A browser for HTTP traffic exercises

---

## Exercise 1 — DNS Query Analysis with NSLookup

### Objective
Capture and analyze a live DNS query using `nslookup` in Command Prompt while Wireshark monitors the traffic in real time. See how a domain name gets resolved to an IP address at the packet level.

### Steps

1. Open **Wireshark** and start a capture on your active network interface
2. Open **Command Prompt** side by side
3. Run the following command:
```
nslookup google.com
```
4. Return to Wireshark and **stop the capture**
5. In the Wireshark filter bar, type the following and press Enter:
```
dns
```
6. Look for the **Standard Query Response** packet for `google.com`
7. Click on it and expand the **Answers** section in the packet details panel

### What to Look For
- The DNS response returning one or more **IP addresses** for `google.com`
- The IP addresses listed under **Answers** in Wireshark will match **exactly** what `nslookup` returned in Command Prompt
- This confirms that both tools are seeing the same DNS resolution in real time

### Key Takeaway
> Every time you visit a website, your computer first sends a DNS query to resolve the domain name into an IP address. Wireshark lets you see this process at the packet level — and it lines up exactly with what nslookup shows you in the command line.

---

## Exercise 2 — Spotting the TCP Three-Way Handshake

### Objective
Capture and identify a live TCP three-way handshake in Wireshark by visiting a website while monitoring traffic in real time. Understand how every connection is established before any data is transferred.

### Steps

1. Open **Wireshark** and start a capture on your active network interface
2. Open a **new browser window** and navigate to:
```
http://zero.webappsecurity.com
```
3. Open **Command Prompt** and run:
```
nslookup zero.webappsecurity.com
```
4. Note the **IP address** returned from nslookup
5. Stop the capture in Wireshark
6. Apply the following filter using the IP address from nslookup:
```
tcp && ip.addr == x.x.x.x
```
7. Look through the packets for the TCP handshake sequence:
   - **SYN** — Your machine initiating the connection
   - **SYN-ACK** — The server acknowledging and responding
   - **ACK** — Your machine confirming the connection is established

### What to Look For
- Three packets in sequence that establish the connection
- The **Flags** column in Wireshark showing `[SYN]`, `[SYN, ACK]`, and `[ACK]`
- This handshake happens before any actual data is transferred

### Key Takeaway
> Every website visit, file download, and login begins with this TCP three-way handshake. Understanding it is fundamental to network analysis, troubleshooting connectivity issues, and identifying suspicious connection attempts during security monitoring.

---

## Exercise 3 — Capturing Credentials over HTTP (Man-in-the-Middle Demo)

### Objective
Demonstrate how unencrypted HTTP connections expose login credentials in plain text — illustrating why HTTP is vulnerable to Man-in-the-Middle (MitM) attacks.

> ⚠️ **Important:** This exercise is performed on a deliberately insecure practice environment only. Never capture or intercept credentials on real systems or sites you do not own. This is for **educational purposes only**.
>
> Practice site used: [http://zero.webappsecurity.com](http://zero.webappsecurity.com)

### Steps

1. Open **Wireshark** and start a new capture
2. Navigate to `http://zero.webappsecurity.com` in a browser and go to the login page
3. Enter test credentials and attempt to log in (login will fail — that's expected)
4. Stop the capture in Wireshark
5. Apply the following filter:
```
http.request.method == "POST"
```
6. Locate the POST packet, click on it, and expand **HTML Form URL Encoded** in the packet details panel

### What to Look For
- The login credentials submitted — visible in **plain text**
- No encryption protecting the data whatsoever
- This is exactly what an attacker on the same network would see in a real MitM attack

### Key Takeaway
> This is why **HTTPS is not optional**. HTTP transmits all data including login credentials in plain text. HTTPS encrypts this data using TLS, making it unreadable to anyone intercepting the traffic. Always verify the padlock icon in your browser before entering any sensitive information.

---

## Exercise 4 — TCP Stream Analysis (Following the Full Conversation)

### Objective
Use Wireshark's **Follow TCP Stream** feature to reconstruct and read the full conversation between your browser and a web server — seeing exactly what was sent and received during a session.

### Steps

1. Open **Wireshark** and start a capture on your active network interface
2. Open a browser and navigate to:
```
http://zero.webappsecurity.com
```
3. Browse the site briefly, then return to Wireshark and **stop the capture**
4. In the filter bar, type:
```
http
```
5. Find an HTTP packet in the results, **right-click** on it
6. Select **Follow** → **TCP Stream**
7. A new window will open showing the full back-and-forth conversation

### What to Look For
- **Red text** — data sent from your browser to the server (the request)
- **Blue text** — data sent from the server back to your browser (the response)
- HTTP headers, page content, and session details all visible in plain text
- The complete request/response cycle laid out in readable form

### Key Takeaway
> Follow TCP Stream lets you reconstruct the entire conversation between a client and server. For HTTP traffic this is completely readable — headers, content, and all. This is a powerful technique used in network forensics, troubleshooting, and security analysis to understand exactly what happened during a session.

---

## Exercise 5 — Saving and Exporting Captures

### Objective
Learn how to save full packet captures and export filtered subsets — an essential skill for documentation, incident response, and sharing findings with a team.

### Part A — Saving the Full Capture

1. After completing any capture, go to **File** → **Save As**
2. Give it a meaningful name, for example:
```
capture101
```
3. Save as a `.pcapng` file — this is Wireshark's standard format
4. This saves **every packet** from the entire capture session

### Part B — Exporting Specific Packets

1. Apply a filter to narrow down to the packets you care about, for example:
```
http
```
2. Go to **File** → **Export Specified Packets**
3. Give it a descriptive name, for example:
```
special_packets
```
4. Choose **Displayed** to export only the filtered packets currently shown
5. Save the file

### Part C — Opening a Saved Capture

1. Go to **File** → **Open**
2. Browse to your saved `.pcapng` file and open it
3. All your previously captured packets load back into Wireshark exactly as they were

### Key Takeaway
> Saving and exporting captures is critical in real security work. Whether you're documenting a lab, preserving evidence of suspicious activity, or sharing findings with a colleague — knowing how to properly save, filter, and reopen captures is a fundamental Wireshark skill.

---

## 📊 Summary

| Exercise | Protocol | Key Concept |
|----------|----------|-------------|
| 1 | DNS | Domain resolution — NSLookup results confirmed in Wireshark |
| 2 | TCP | Three-way handshake — SYN, SYN-ACK, ACK |
| 3 | HTTP POST | Plaintext credential exposure / MitM demo |
| 4 | HTTP / TCP | Follow TCP Stream — full browser to server conversation |
| 5 | N/A | Saving, exporting, and reopening packet captures |

---

## 🎓 Real World Relevance

These exercises directly map to skills used in:

- **SOC Analysis** — Monitoring network traffic for anomalies
- **Penetration Testing** — Identifying insecure protocols in use
- **Cloud Security** — Understanding VPC flow logs and network monitoring
- **IT Troubleshooting** — Diagnosing connectivity and DNS issues

---

## 📹 Lab Walkthrough Video

*Video walkthrough recorded via Loom — link coming soon.*

---

## 🔗 Connect

- [LinkedIn](https://linkedin.com)
- [GitHub](https://github.com)

> *This lab was created for educational and portfolio purposes as part of an ongoing cybersecurity skill development journey.*
