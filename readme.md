# Axios Supply Chain Attack Detection — Sigma Rule Set

This repository contains a **Sigma-based detection pack** for identifying behaviors associated with the **March 2026 Axios npm supply chain compromise**.

The goal of this project is to demonstrate **behavioral detection engineering**, not just IOC matching — focusing on how the attack actually operates across process, network, and file activity.

---

## 🧠 Overview of the Attack

In March 2026, malicious versions of the popular npm package Axios were briefly published and distributed.

These versions:
- Executed a **`postinstall` script** during package installation
- Performed **OS fingerprinting** using Node.js APIs
- Downloaded **platform-specific payloads**
- Established outbound communication to attacker-controlled infrastructure
- Attempted **anti-forensic cleanup** after execution

This attack is a classic example of a **software supply chain compromise**.

---

## 🎯 Detection Philosophy

This rule set is designed to detect the **behavioral chain**, not just static indicators.

Instead of relying only on:
- IP addresses
- domains
- package names

These rules detect:

> **Install-time execution → OS discovery → process spawning → network egress → payload artifacts**

This approach is more resilient to:
- infrastructure changes
- obfuscation
- variant attacks

---

## 📁 Repository Structure

```
sigma/
├── axios_install_chain.yml
├── axios_c2_detection.yml
├── axios_payload_artifacts.yml
├── axios_runtime_behavior.yml
├── axios_network_behavior.yml
└── axios_self_delete.yml
```

Each rule focuses on a specific stage of the attack chain.

---

## 🔍 What These Rules Detect

### 1. Install-Time Execution Abuse
- Node/npm spawning shell or scripting engines
- Abuse of `postinstall` hooks

### 2. OS Fingerprinting Behavior
- Node processes accessing system identity information

### 3. Suspicious Process Execution
- PowerShell, Python, bash, or script engines launched from Node

### 4. Network Activity
- Outbound connections during install phase
- Known campaign infrastructure:
  - `sfrclak[.]com`
  - `142.11.206.73`
  - TCP port `8000`

### 5. Payload Artifacts
- Known file paths used by the campaign:
  - `/tmp/ld.py`
  - `%PROGRAMDATA%\wt.exe`
  - `/Library/Caches/com.apple.act.mond`

### 6. Anti-Forensic Behavior
- Deletion or modification of `node_modules` content

---

## 🧩 What is Sigma?

Sigma is a platform-agnostic rule format for describing security detections.

Sigma rules:
- Define **what suspicious behavior looks like**
- Can be converted into queries for SIEM platforms such as:
  - Microsoft Sentinel
  - Splunk
  - Elastic Security

---

## 🔄 How to Use These Rules

### 1. Convert Sigma → SIEM Query

Use the Sigma converter:

```bash
sigmac -t sentinel rule.yml
```

or for Splunk:

```bash
sigmac -t splunk rule.yml
```

---

### 2. Deploy in Your SIEM

- Import converted queries into your SIEM
- Tune based on your environment
- Set alert thresholds and suppression rules as needed

---

### 3. Validate Detection Coverage

Test against:
- npm install activity
- Node-based applications
- lab simulations of the attack chain

---

## 🛡️ MITRE ATT&CK Mapping

| Technique   | Description                         |
|------------|-------------------------------------|
| T1195.002  | Supply Chain Compromise             |
| T1059      | Command and Scripting Interpreter   |
| T1105      | Ingress Tool Transfer               |
| T1082      | System Information Discovery        |

---

## ⚠️ Important Notes

- These rules are marked as **experimental**
- Some detections may generate **false positives**
- Environment-specific tuning is required
- Effectiveness depends on available telemetry:
  - Process creation logs
  - Network connection logs
  - File activity logs

---

## 🔧 Recommended Log Sources

For best results, ensure visibility into:

- Process creation events (e.g., Sysmon Event ID 1)
- Network connections (e.g., Sysmon Event ID 3)
- File creation/modification (e.g., Sysmon Event ID 11)

---

## 🎯 Use Cases

- Detection engineering labs
- SIEM rule development
- Supply chain threat detection research
- Blue team training and simulations
- Security portfolio projects

---

## 🧑‍💻 Author

**JT Wilson**

---

## 📚 References

- https://snyk.io/blog/axios-npm-package-compromised-supply-chain-attack-delivers-cross-platform/

---

## 📜 Disclaimer

These rules are based on publicly available information about the Axios supply chain attack.  
They are provided for educational and defensive purposes only and should be validated and tuned before production use.

---

## ⭐ Summary

This project demonstrates how to detect modern supply chain attacks by focusing on:

> **Behavioral patterns instead of static indicators**

— a key principle in effective detection engineering.
