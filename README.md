### Network-Analyzer
Real-Time Network Monitoring, Threat Detection &amp; Traffic Intelligence

A lightweight Python-based network monitoring and behavioral intrusion detection project designed to observe live traffic, extract useful packet information, detect suspicious network patterns, and present security insights through a terminal-based dashboard.

## 📌 Project Summary

**Network Analyzer** is a command-line network security application built for educational labs, authorized network monitoring, cybersecurity experimentation, and portfolio demonstration.

The application listens to live network traffic, processes packet metadata, calculates traffic statistics, evaluates predefined security heuristics, assigns risk levels to detected events, and provides a real-time view of network activity.

It can additionally preserve session information in **CSV format** and export captured packets as **PCAP files**, allowing the captured traffic to be investigated later with tools such as Wireshark.

> ⚠️ **Important:** Use this application only on networks, devices, and interfaces that you own or have explicit permission to monitor.

# 🎯 Why This Project?

Network traffic can contain thousands of packets within a short period of time. Manually examining this traffic is difficult and inefficient.

This project addresses that problem by creating a lightweight monitoring pipeline capable of:

- Observing network traffic continuously
- Summarizing packet behavior
- Identifying unusual communication patterns
- Highlighting potentially suspicious sources
- Assigning a consistent risk score
- Grouping related security events
- Preserving data for later investigation

The goal is not to replace a professional enterprise IDS, but to demonstrate how fundamental **network security, packet analysis, behavioral detection, and security monitoring** concepts can be implemented in Python.

# 🧠 Core Concept

The application follows a continuous analysis cycle:

```text
                LIVE NETWORK TRAFFIC
                        │
                        ▼
                ┌───────────────┐
                │ Packet Capture │
                └───────┬───────┘
                        │
                        ▼
                ┌───────────────┐
                │ Packet Parsing │
                └───────┬───────┘
                        │
             ┌──────────┴──────────┐
             ▼                     ▼
       Traffic Metrics       Detection Engine
             │                     │
             │                     ▼
             │               Risk Evaluation
             │                     │
             └──────────┬──────────┘
                        ▼
                Incident Correlation
                        │
                        ▼
                 Terminal Dashboard
                        │
                ┌───────┴────────┐
                ▼                ▼
             CSV Logs         PCAP File
```

# ✨ Major Capabilities

## 🔴 Live Traffic Observation

The analyzer can capture packets from an available network interface and process them during the active monitoring session.

Supported traffic analysis includes:

- TCP
- UDP
- ICMP
- Other IP-based traffic

## 📊 Traffic Statistics

The application continuously maintains useful traffic metrics, including:

- Packets per second
- Bytes per second
- Average packet size
- Protocol distribution
- Frequently communicating hosts
- Top network talkers
- Live packet activity

These statistics provide a quick overview of the current network environment.

## 🚨 Behavioral Threat Detection

Instead of relying on machine learning, the application uses predefined behavioral rules.

The detection engine looks for network patterns that may deserve investigation.

Current detection identifiers include:

| ID | Detection | Purpose |
|---|---|---|
| `NET-001` | TCP SYN Scan | Detects a source sending SYN packets toward many destination ports |
| `NET-002` | Port Scan | Identifies communication with many ports on a target |
| `NET-003` | ICMP Sweep | Detects ICMP probing across multiple destination hosts |
| `NET-004` | Abnormal SYN Activity | Identifies unusually high SYN activity |
| `NET-005` | Traffic Spike | Detects traffic rates significantly above the rolling baseline |
| `NET-006` | DNS Request Anomaly | Detects unusually high DNS request activity |
| `NET-007` | Unusual Port Activity | Identifies repeated communication involving uncommon ports |

### Detection Philosophy

These rules should be considered **indicators**, not definitive evidence of an attack.

For example, a large number of connections may be completely legitimate on a busy server.

Therefore:

```text
Detection ≠ Confirmed Attack

The purpose of the detection engine is to help an analyst identify activity that deserves further investigation.

# 🎚️ Risk Assessment

Each generated alert receives a deterministic score between **0 and 100**.

The score begins with a rule-specific base value and can increase when additional evidence strengthens the observed behavior.

Examples of supporting evidence include:

- Larger numbers of destination ports
- More contacted hosts
- Higher event counts
- Multiple detection rules associated with one source

### Risk Categories

| Score | Classification |
|---:|---|
| `0–29` | 🟢 Low |
| `30–59` | 🟡 Medium |
| `60–79` | 🟠 High |
| `80–100` | 🔴 Critical |

The risk score is intended for **alert prioritization**.

It is not:

- A probability of compromise
- A machine-learning prediction
- Proof that malicious activity occurred

# 🔎 Incident Correlation

Individual alerts may represent parts of the same larger activity.

The project therefore includes an incident-correlation layer that helps group related alerts.

Conceptually:

```text
Alert A ──┐
Alert B ──┼──► Related Activity ──► Incident
Alert C ──┘
```

This makes it easier to view multiple related security signals together instead of treating every alert as an isolated event.

# 👀 Suspicious Host Watchlist

Recently suspicious sources can be maintained through a watchlist.

This provides a quick way to identify hosts that repeatedly appear in detection events during a monitoring session.

The watchlist can support investigation by answering questions such as:

- Which source generated recent alerts?
- Is the same host triggering multiple rules?
- Is suspicious activity continuing?
- Are several alerts connected to one source?

# 🖥️ Terminal Dashboard

The application uses the **Rich** Python library to provide a structured terminal interface.

The dashboard can present information such as:

### Network Activity

- Current packet activity
- Traffic rates
- Protocol information
- Top communicating hosts

### Security Activity

- Detection events
- Risk levels
- Suspicious hosts
- Related incidents

### Session Information

- Live packet stream
- Monitoring status
- Export status

An optional IP-masking feature is also available for demonstrations, presentations, and screen recordings where displaying real addresses is undesirable.

# 💾 Evidence & Export

The application can preserve information generated during a monitoring session.

## CSV Output

Session information is written to:

```text
logs/session_events.csv
```

The CSV can contain:

- Packet metadata
- Detection information
- Alert summaries
- Session-related records

CSV output is useful for:

- Later analysis
- Reporting
- Data processing
- Academic demonstrations

## PCAP Capture

Captured network traffic can be stored at:

```text
captures/session_capture.pcap
```

PCAP files can be opened with network-analysis tools such as **Wireshark** for deeper investigation.

This creates a useful workflow:

```text
Live Detection
      ↓
Capture Evidence
      ↓
Save PCAP
      ↓
Open in Wireshark
      ↓
Perform Detailed Investigation
```

# 🏗️ Application Architecture

The project is organized into separate functional layers.

```text
┌──────────────────────────────┐
│         main.py              │
│    Application Entry Point   │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│       Packet Capture         │
│       core/capture.py        │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│       Packet Analyzer        │
│       core/analyzer.py       │
└──────────────┬───────────────┘
               │
        ┌──────┴──────┐
        ▼             ▼
┌─────────────┐  ┌─────────────────┐
│ Statistics  │  │ Detection Engine│
│             │  │                 │
└─────────────┘  └────────┬────────┘
                          │
                          ▼
                  ┌───────────────┐
                  │ Risk Scoring  │
                  └───────┬───────┘
                          │
                          ▼
                  ┌───────────────┐
                  │   Incident    │
                  │  Correlation  │
                  └───────┬───────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │ Rich Dashboard  │
                 └────────┬────────┘
                          │
                    ┌─────┴─────┐
                    ▼           ▼
                   CSV         PCAP
```

# 📂 Repository Organization

```text
Network-traffic-analyzer/
│
├── main.py
├── config.py
├── requirements.txt
│
├── core/
│   ├── capture.py
│   ├── analyzer.py
│   └── statistics.py
│
├── detection/
│   ├── detection rules
│   ├── risk scoring
│   └── watchlist handling
│
├── incident/
│   └── incident correlation
│
├── dashboard/
│   └── Rich terminal interface
│
├── exporters/
│   ├── CSV output
│   └── PCAP output
│
├── tests/
│   └── synthetic packet tests
│
├── logs/
│   └── session_events.csv
│
└── captures/
    └── session_capture.pcap
```

The repository separates **capture, analysis, detection, incident handling, visualization, exporting, and testing**, making the project easier to understand and extend.

# ⚙️ Technology Stack

| Technology | Role |
|---|---|
| **Python** | Main development language |
| **Scapy** | Packet capture and packet processing |
| **Rich** | Terminal dashboard and formatted output |
| **CSV** | Session event storage |
| **PCAP** | Packet capture preservation |
| **Wireshark** | Optional packet investigation |
| **unittest** | Automated testing |
| **Git / GitHub** | Source-code management |

# 🚀 Getting Started

## Prerequisites

Make sure Python 3 is installed.

You will also need the appropriate packet-capture permissions and support for your operating system.

## 1. Clone the Repository

```bash
git clone https://github.com/Shubh070705/Network-traffic-analyzer.git
```

Enter the project:

```bash
cd Network-traffic-analyzer
```
## 2. Create a Virtual Environment

```bash
python3 -m venv .venv
```

Activate it on Linux/macOS:

```bash
source .venv/bin/activate
```

On Windows:

```powershell
.venv\Scripts\activate
```

## 3. Install Dependencies

```bash
pip install -r requirements.txt
```

# ▶️ Starting the Analyzer

The standard command is:

```bash
python3 main.py
```

For systems where elevated privileges are required:

```bash
sudo python3 main.py
```

# 🧰 Command-Line Options

### Display available interfaces

```bash
python3 main.py --list-interfaces
```

### Monitor a specific interface

```bash
python3 main.py --interface eth0
```

### Apply a BPF filter

```bash
python3 main.py --interface eth0 --bpf "tcp or udp"
```

### Hide IP addresses

```bash
python3 main.py --interface eth0 --mask-ips
```

### Limit captured packets

```bash
python3 main.py --interface eth0 --max-packets 5000
```

### Customize detection thresholds

```bash
python3 main.py \
  --interface eth0 \
  --syn-scan-threshold 10 \
  --port-scan-threshold 25
```

### Disable PCAP generation

```bash
python3 main.py --interface eth0 --no-pcap
```

### Disable CSV logging

```bash
python3 main.py --interface eth0 --no-csv
```

Stop a running session with:

```text
Ctrl + C
```

# 🖥️ Platform Considerations

## Linux

Live packet capture may require administrator privileges.

Example:

```bash
sudo python3 main.py
```

An alternative is to grant the required capabilities to the Python interpreter:

```bash
sudo setcap cap_net_raw,cap_net_admin=eip $(readlink -f $(which python3))
```

## Windows

Windows packet capture requires **Npcap** in WinPcap API-compatible mode.

The terminal may need to be launched with administrator privileges.

## macOS

Depending on the interface and packet-capture configuration, administrator permissions may be required.

# 🧪 Testing

The project includes automated tests.

Run:

```bash
python3 -m unittest discover -s tests -v
```

The testing approach uses **synthetic packet records** to test statistics and detection behavior without depending entirely on live network traffic.

Packet parsing tests may be skipped when Scapy is unavailable.


# 🔬 What Can Be Investigated?

This project can be used to study several cybersecurity concepts.

### Network Reconnaissance

Analyze patterns associated with:

- SYN scanning
- Port scanning
- ICMP probing

### Traffic Abnormalities

Investigate:

- Sudden traffic increases
- Unusual SYN activity
- Unexpected DNS volume

### Host Behavior

Observe:

- Frequent network talkers
- Suspicious sources
- Repeated rule violations

### Protocol Behavior

Understand the distribution and activity of:

- TCP
- UDP
- ICMP
- Other IP traffic

# 📈 Example Monitoring Scenario

A typical monitoring session can follow this sequence:

```text
1. Select network interface
          ↓
2. Start packet capture
          ↓
3. Normalize packet metadata
          ↓
4. Update traffic statistics
          ↓
5. Evaluate detection rules
          ↓
6. Generate alerts
          ↓
7. Calculate risk score
          ↓
8. Correlate related alerts
          ↓
9. Display results
          ↓
10. Save CSV / PCAP evidence
```

This makes the project useful as a compact demonstration of a basic **network detection and monitoring pipeline**.


# ⚠️ Detection & Security Disclaimer

The detection system is intentionally heuristic.

It does **not** perform:

- Full malware analysis
- Payload-based deep inspection
- Machine-learning classification
- Guaranteed attack identification
- Complete enterprise-level intrusion prevention

A detected event means:

> **"This traffic pattern matches a rule and should be investigated."**

It does not automatically mean:

> **"The network has been compromised."**

False positives are therefore possible.

# 🔐 Privacy & Responsible Use

Network traffic can contain sensitive information.

When using this project:

- Monitor only authorized interfaces.
- Do not capture traffic from networks without permission.
- Protect generated PCAP files.
- Avoid publicly sharing sensitive packet captures.
- Use IP masking when demonstrating the project publicly.
- Treat captured network information as potentially confidential.

# 🚧 Current Constraints

The current implementation has several practical limitations:

### Heuristic Detection

Rules can produce false positives because they identify behavioral patterns rather than confirming malicious intent.

### Header-Oriented Analysis

The analyzer focuses on packet headers and metadata for statistics and detection rather than inspecting packet payloads.

### PCAP Storage

PCAP export preserves complete packets so that they can be investigated using compatible analysis tools.

### Platform Dependencies

Live monitoring depends on:

- Operating-system permissions
- Scapy support
- libpcap/Npcap availability
- Correct interface configuration

### Offline Analysis

A dedicated offline-PCAP investigation workflow is not currently implemented.

### Threshold Tuning

Detection thresholds may require adjustment depending on the characteristics and traffic volume of the monitored network.

---

# 🔮 Potential Improvements

The project can be expanded significantly in future versions.

## 1. Offline PCAP Analysis

Add a mode for loading previously captured PCAP files and applying the same detection pipeline.

## 2. Advanced Detection

Introduce additional behavioral rules for more network activity patterns.

## 3. Machine Learning

A future version could experiment with ML-based anomaly detection while retaining the existing heuristic engine for explainability.

## 4. Web Dashboard

Replace or complement the terminal dashboard with a browser-based interface.

## 5. Database Storage

Store alerts and session metadata in a database for historical analysis.

## 6. Alert Notifications

Add controlled integrations for security notifications.

## 7. Historical Analytics

Track network behavior across multiple monitoring sessions.

## 8. Better Incident Management

Provide richer incident timelines and relationships between alerts.

## 9. Configuration Profiles

Create different threshold profiles for:

- Home networks
- Laboratories
- Servers
- High-traffic environments

---

# 📚 Educational Value

This project brings several cybersecurity concepts together in one implementation.

### Networking

- Packet communication
- IP traffic
- TCP/UDP behavior
- ICMP
- DNS activity
- Ports and hosts

### Cybersecurity

- Network monitoring
- Intrusion detection concepts
- Reconnaissance patterns
- Anomaly detection
- Risk prioritization
- Incident correlation

### Python Development

- Modular architecture
- Packet processing
- Threaded capture
- Data structures
- CLI development
- File export
- Automated testing

---

# 🎓 Learning Outcomes

After working with this project, a learner can gain practical understanding of:

- How live packets can be captured programmatically
- How packet metadata can be normalized
- How network statistics can be calculated
- How behavioral rules can identify suspicious patterns
- How security alerts can be prioritized
- How multiple alerts can be correlated
- How network evidence can be exported
- How PCAP files can support further investigation
- How a modular security application can be structured
- How automated tests can validate detection logic

---

# 🏆 Project Highlights

### Networking

**Live packet capture + protocol analysis**

### Detection

**7 behavioral detection rules**

### Risk Management

**Deterministic 0–100 alert scoring**

### Monitoring

**Rolling traffic statistics and suspicious-host tracking**

### Investigation

**CSV event records + PCAP evidence**

### Interface

**Real-time Rich terminal dashboard**

### Testing

**Synthetic packet-based unit testing**

---

# 📌 Project Status

**Current Status:** Functional educational / portfolio project

The application currently provides the core workflow required for:

```text
Capture
  ↓
Analyze
  ↓
Detect
  ↓
Score
  ↓
Correlate
  ↓
Display
  ↓
Export
```

---

# 👨‍💻 Author

##  ANKUSH KUMAR BITTU

Network Analyzer was developed as a learning and portfolio-oriented cybersecurity project.

---

# ⭐ Support the Project

If you find this project useful for learning about network monitoring or cybersecurity development:

- ⭐ Star the repository
- 🍴 Fork it for experimentation
- 🐛 Report reproducible issues
- 💡 Suggest improvements
- 🔧 Contribute enhancements

---

# 📜 License & Usage

Refer to the repository's license information for the applicable terms.

This project is intended for **authorized security research, education, laboratory experimentation, and legitimate network monitoring**.

---

## 🔖 Topics

```text
python
network-security
cybersecurity
network-monitoring
intrusion-detection
nids
packet-analysis
scapy
network-analyzer
traffic-analysis
threat-detection
risk-scoring
incident-correlation
wireshark
pcap
network-traffic
security-monitoring
ethical-hacking
cybersecurity-project
python-project
```
