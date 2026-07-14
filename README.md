# Home SOC Lab: RDP Brute-Force Detection

A hands-on security lab built to simulate a real-world attack, detect it using a SIEM, and build an automated alert — replicating the core workflow of a SOC (Security Operations Center) analyst.

## What This Project Demonstrates

- Building an isolated virtual lab environment (attacker + victim VMs)
- Installing and configuring Splunk Enterprise as a SIEM
- Simulating a brute-force attack against RDP using Hydra
- Writing a detection query (SPL) to identify the attack from raw logs
- Building and validating a scheduled Splunk alert
- Real troubleshooting: diagnosing a network misconfiguration that was silently blocking detection

## Lab Architecture

```
┌─────────────────────┐         ┌─────────────────────┐
│   Kali Linux (VM)    │         │  Windows 11 (VM)     │
│   Attacker           │ ──────► │  Victim + Splunk     │
│   192.168.128.3      │  RDP    │  192.168.128.2       │
└─────────────────────┘         └─────────────────────┘
        Host-Only Network (fully isolated, no internet)
```

## Tools Used

| Tool | Purpose |
|---|---|
| UTM | Virtualization (Apple Silicon) |
| Kali Linux | Attack platform |
| Windows 11 | Target system |
| Hydra | Brute-force attack simulation |
| Nmap | Network/port reconnaissance |
| Splunk Enterprise | SIEM — log collection, search, alerting |

## Files in This Repository

- [`incident-report.md`](./incident-report.md) — Full writeup of the attack, detection, and response, including MITRE ATT&CK mapping
- `/screenshots` — Supporting evidence (Splunk searches, alert configuration, attack execution)

## Key Detection Query

```spl
index=main LogName=Security EventCode=4625
| stats count by Account_Name, Source_Network_Address
| where count >= 3
```

This flags accounts with 3+ failed login attempts from the same source IP — a pattern consistent with automated password guessing.

## What I Learned

Beyond the technical setup, this project involved genuine troubleshooting: failed logon events weren't reaching Splunk, and the root cause turned out to be a Windows network profile misconfiguration silently blocking RDP traffic at the firewall level — not an audit policy or Splunk issue as initially suspected. Diagnosing this required working through the problem layer by layer (network connectivity → firewall rules → network profile → Group Policy), which reflects the kind of methodical investigation used in real security incident response.

## Next Steps

- [ ] Add a second attack scenario (e.g., SSH brute-force or port scan detection)
- [ ] Map additional detections to MITRE ATT&CK
- [ ] Add Wazuh for endpoint detection
- [ ] Build a Splunk dashboard for visualizing failed login trends
