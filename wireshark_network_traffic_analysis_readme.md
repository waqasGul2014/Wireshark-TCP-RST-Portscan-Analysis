# Suggested Repository Name: `wireshark-tcp-rst-portscan-analysis`
*(Alternative names: `network-traffic-analysis-wireshark` or `soc-packet-analysis-port-scanning`)*

---

# Network Traffic Analysis: Detecting Port Scanning & TCP RST Anomalies using Wireshark

## 📌 Project Overview
This hands-on cybersecurity lab project focuses on packet-level network traffic analysis using **Wireshark**. The objective is to capture, inspect, and differentiate normal network baseline behavior from suspicious reconnaissance and anomalous packet spikes—specifically **Port Scanning** (such as Nmap SYN/Connect scans) and high-frequency **TCP `[RST, ACK]`** traffic bursts.

---

## 🎯 Objectives
* Capture live network packets in a controlled lab environment.
* Analyze TCP headers, control flags (`SYN`, `RST`, `ACK`), and frame timestamps at the millisecond level.
* Identify indicators of port scanning and potential TCP Reset flooding.
* Document actionable Wireshark display filters and SOC detection takeaways.

---

## 🛠️ Tools & Environment
* **Packet Analyzer:** Wireshark
* **Traffic Generation / Simulation:** Controlled Lab Environment (Nmap / Local Network Simulation)
* **Focus Area:** Blue Team, SOC Tier-1/Tier-2 Analysis, Threat Hunting, Packet Inspection

---

## 🔍 Key Findings & Packet Analysis

### 1. High-Frequency TCP `[RST, ACK]` Spikes
During packet inspection, a rapid burst of `TCP [RST, ACK]` packets was observed within millisecond intervals between the source (`192.168.1.1`) and destination (`10.0.2.15`). 
* **Technical Context:** When a host sends connection requests (`SYN`) to multiple closed ports during a port scan, the target system responds with `TCP [RST, ACK]` packets to reject the connection. An abnormal spike in these reset responses within a very short timeframe is a strong indicator of active port enumeration or connection disruption.

### 2. Baseline vs. Anomalous Traffic
* **Normal Baseline:** Gradual TCP three-way handshakes (`SYN` $\rightarrow$ `SYN, ACK` $\rightarrow$ `ACK`) with varied payloads and longer intervals between distinct sessions.
* **Anomalous Pattern:** Repetitive, uniform packet lengths (`60` bytes) with sequential port probing and immediate `RST, ACK` responses occurring milliseconds apart.

---

## 🧪 Wireshark Display Filters Used

You can use the following display filters in Wireshark to isolate similar traffic patterns:

```text
# Isolate all TCP Reset + Acknowledgment responses
tcp.flags.reset == 1 && tcp.flags.ack == 1

# Filter traffic between specific source and destination hosts
ip.addr == 192.168.1.1 && ip.addr == 10.0.2.15 && tcp

# Detect potential SYN scan attempts (SYN set, ACK not set)
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

---

## 📸 Packet Capture Evidence

*(Note: Upload your two screenshots into an `images/` folder in your GitHub repository and name them `capture-1.png` and `capture-2.png` so they render automatically below.)*

### Capture 1: Millisecond-Interval TCP `[RST, ACK]` Burst
![TCP RST ACK Burst Capture 1](images/capture-1.png)

### Capture 2: Frame & Packet Header Inspection
![Packet Header and Frame Details Capture 2](images/capture-2.png)

---

## 🛡️ SOC Analyst Takeaways & Mitigation
* **SIEM / IDS Alerting:** Configure threshold-based alerts for high counts of `TCP RST` packets originating from or targeting a single internal IP within a 1-second window.
* **Network Hardening:** Implement host-based firewalls and rate-limiting rules to drop unsolicited probes on closed ports silently rather than generating excessive reset traffic.

---

## 📂 Recommended Repository Structure
```text
wireshark-tcp-rst-portscan-analysis/
├── images/
│   ├── capture-1.png
│   └── capture-2.png
└── README.md
```