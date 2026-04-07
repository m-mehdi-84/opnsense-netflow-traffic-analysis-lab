# OPNsense NetFlow Traffic Analysis Lab

A hands-on cybersecurity lab built using Hyper-V and OPNsense to explore traffic monitoring, baseline analysis, performance observation, and IDS verification in a virtual environment.

This lab demonstrates how to use NetFlow and Traffic Graph in OPNsense to observe network activity, compare normal traffic with increased traffic, and verify IDS functionality through controlled testing.

* * *

## Lab Overview

This lab simulates a small internal network monitored by OPNsense.

Components:

- Hyper-V virtualization
- OPNsense firewall/router
- Windows Server
- Windows 11 client

* * *

## Network Topology

                INTERNET
                    |
               [ WAN - hn0 ]
                    |
             +----------------+
             |    OPNsense    |
             | Firewall/Router|
             +--------+-------+
                      |
                [ LAN - hn1 ]
                      |
               +---------------+
               |   LAN-Switch   |
               +-------+--------+
                       |
          +------------+------------+
          |                         |
  Windows Server               Windows 11
  192.168.10.145              192.168.10.144
  GW: 192.168.10.1            GW: 192.168.10.1

* * *

## Network Design

| Network | Subnet | Gateway | Purpose |
|--------|--------|---------|---------|
| LAN | 192.168.10.0/24 | 192.168.10.1 | Main internal lab network |

* * *

## Key Features

- NetFlow setup in OPNsense
- Traffic monitoring with Reporting > Traffic
- Baseline traffic observation
- Increased traffic testing between client and server
- IDS setup using Intrusion Detection
- User defined IDS rule testing
- Suricata log verification
- Hyper-V lab troubleshooting and traffic behavior analysis

* * *

## Security Concept

This lab demonstrates two main monitoring layers:

- NetFlow and Traffic Graph help observe traffic patterns and bandwidth activity
- IDS helps detect specific traffic that matches active rules and generates alerts

The lab also shows an important practical detail: traffic inside the same LAN does not always pass through OPNsense in a way that matches expectations for inspection. Because of that, direct testing against OPNsense was used to verify IDS functionality.

* * *

## Configuration Summary

### NetFlow / Traffic

- Enabled NetFlow on the LAN interface
- Opened Reporting > Traffic to observe traffic activity
- Generated normal traffic between the client and server
- Used the results as a simple baseline
- Generated increased traffic with large ping packets from Windows 11 to the Windows Server
- Observed higher activity in Traffic Graph and Top Talkers

### IDS

- Enabled Intrusion Detection
- Selected the LAN interface
- Downloaded and applied ET open rules
- Tested several common methods, including ping and testmyids
- Verified that the initial tests did not generate alerts in the current rule and policy setup
- Created a user defined ICMP alert rule from the client to OPNsense
- Verified alert generation successfully

### Log Verification

- Confirmed that Suricata started correctly
- Verified rule reload activity
- Verified that the ICMP alert was recorded in the Suricata log
- Confirmed traffic was captured on the LAN interface without reported packet drops in the shown log output

* * *

## Verification

| Test | Result |
|------|--------|
| Win11 IP assignment | 192.168.10.144 |
| Server IP assignment | 192.168.10.145 |
| Ping Win11 → Server | Success |
| NetFlow traffic visible on LAN | Success |
| Top Talkers visible | Success |
| Increased traffic visible in graph | Success |
| Initial IDS test alerts | No new alerts |
| User defined IDS alert to OPNsense | Success |
| Suricata log verification | Success |

* * *

## Screenshots

### 1. NetFlow Settings

Shows NetFlow enabled on the LAN interface in OPNsense.

### 2. Reporting > Traffic

Shows traffic activity collected from the LAN interface.

### 3. Top Talkers

Shows active IP addresses observed during traffic generation.

### 4. Traffic Graph

Shows the difference between normal traffic and increased traffic.

### 5. IDS Settings

Shows Intrusion Detection enabled on the LAN interface.

### 6. Rules / Policy View

Shows the downloaded rules and active IDS policy used during testing.

### 7. User Defined IDS Rule

Shows the custom ICMP alert rule used to verify IDS functionality.

### 8. IDS Alert

Shows the alert generated after pinging OPNsense from the client.

### 9. Suricata Log File

Shows Suricata startup, rule reload activity, and the logged ICMP alert.

* * *

## Documentation

Detailed step-by-step guide is available in:

`docs/lab-documentation.md`

Includes:

- Lab preparation
- NetFlow setup
- Baseline traffic testing
- Increased traffic testing
- IDS configuration
- Controlled alert verification
- Troubleshooting
- Validation and analysis

* * *

## What I Learned

- How to enable and use NetFlow in OPNsense for traffic monitoring
- How to observe baseline traffic and compare it with increased traffic
- How to use Traffic Graph and Top Talkers for quick analysis
- Why traffic inside the same subnet may not behave as expected for firewall and IDS testing
- How to verify IDS functionality with a controlled user defined rule
- How to confirm IDS events in the Suricata log

* * *

## Key Skills Demonstrated

- OPNsense configuration
- NetFlow and traffic analysis
- IDS setup and verification
- Hyper-V network troubleshooting
- Traffic pattern observation
- Security testing and validation
- Technical documentation

* * *

## Author

Muhammad Mehdi  
IT Security Developer Student
