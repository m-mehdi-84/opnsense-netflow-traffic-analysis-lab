# OPNsense NetFlow Traffic Analysis Lab Documentation

## Overview

This lab was built in Hyper-V using OPNsense, Windows Server, and Windows 11 to test traffic monitoring, baseline analysis, increased traffic observation, and IDS verification.

The goal was to understand how to:

- observe normal traffic
- identify increased traffic in OPNsense
- compare NetFlow-based monitoring with IDS-based detection
- verify IDS functionality in a controlled way

The lab also highlighted an important real-world detail: traffic between devices in the same LAN does not always behave as expected from a firewall or IDS testing perspective.

---

## Lab Environment

### Virtual Machines

- OPNsense-VM
- Server2025-VM
- Win11-VM

### Interfaces

- WAN -> Default Switch
- LAN -> LAN-Switch

### IP Addresses

- OPNsense LAN: 192.168.10.1/24
- Windows Server: 192.168.10.145
- Windows 11: 192.168.10.144

---

## Objective

The main tasks in the lab were:

1. Prepare the environment
2. Enable NetFlow and observe normal traffic
3. Generate increased traffic and compare the result
4. Enable and test IDS
5. Verify alert generation
6. Analyze the result

---

## Step 1 - Prepare the Environment

The first step was to verify that the lab environment was working correctly before any monitoring was configured.

### Validation

- OPNsense LAN confirmed as `192.168.10.1/24`
- Windows Server received `192.168.10.145`
- Windows 11 received `192.168.10.144`
- Ping between systems worked

This confirmed that the basic LAN communication was working.

---

## Step 2 - Enable NetFlow and Observe Baseline Traffic

The next step was to enable NetFlow on OPNsense and begin observing traffic on the LAN interface.

### OPNsense Configuration

- Opened `Reporting > NetFlow`
- Selected the `LAN` interface
- Enabled local capture
- Applied the configuration

### Traffic Observation

- Opened `Reporting > Traffic`
- Waited for traffic data to appear
- Generated simple traffic between the Windows 11 client and the Windows Server
- Observed activity in the graph and Top Talkers

### Result

Traffic began to appear in OPNsense and the Windows 11 client was visible in the monitored traffic. This was used as a simple baseline for normal traffic in the environment.

---

## Step 3 - Generate Increased Traffic

After baseline traffic had been observed, the next step was to create more traffic and compare the result.

### Test

From Windows 11, large ICMP traffic was sent toward the Windows Server.

Example command:
`ping 192.168.10.145 -t -l 65000`

### Observation

- Traffic Graph showed a visible increase compared with the baseline
- Top Talkers showed the client and server during the test
- The results sometimes appeared with a short delay, which is reasonable in a Hyper-V-based virtual lab

### Result

The increased traffic was successfully visible in OPNsense. However, the environment did not show major performance degradation. Follow-up testing from the server still showed very low latency and no packet loss.

---

## Step 4 - Enable IDS

After traffic monitoring was confirmed, the next step was to activate Intrusion Detection.

### IDS Configuration

- Opened `Services > Intrusion Detection`
- Enabled IDS
- Selected `PCAP live mode (IDS)`
- Selected the `LAN` interface
- Downloaded ET open rules
- Applied the configuration

### Initial Testing

Several common tests were tried:

- large ping traffic between client and server
- ping from the client to OPNsense
- `curl http://testmyids.com`
- `nmap -sS -Pn 192.168.10.1`

### Result

No new alerts were generated during the initial tests, even though IDS was active and rules were loaded.

---

## Step 5 - Troubleshooting and Controlled Verification

Because the initial tests did not generate clear IDS alerts, the environment was analyzed more closely.

### Observation

The Windows client and Windows Server were in the same subnet:

- Windows 11 -> `192.168.10.144`
- Windows Server -> `192.168.10.145`

This meant that some traffic between them could stay inside the LAN path and not behave the way a routed firewall inspection test would normally be expected to behave.

### Controlled IDS Rule

To verify IDS in a more reliable way, a user defined rule was created for ICMP traffic from the Windows 11 client to OPNsense.

Configuration used:

- Source IP: `192.168.10.144`
- Destination IP: `192.168.10.1`
- Action: `Alert`
- Description: `Test ICMP to OPNsense`

### Verification

Traffic was then generated from Windows 11 toward OPNsense to verify that the custom test rule produced an alert.

### Result

This generated an alert successfully in OPNsense and confirmed that IDS events were being detected and logged in the environment.

---

## Step 6 - Suricata Log Verification

After the alert appeared in the OPNsense interface, the Suricata log was reviewed.

### Log Confirmation

The log showed:

- Suricata started correctly
- rule reload activity completed successfully
- the engine was running on the LAN interface
- packet capture activity was visible
- the ICMP test alert was logged

Example result:

- `Test ICMP to OPNsense`
- Source: `192.168.10.144`
- Destination: `192.168.10.1`

### Result

This confirmed that IDS was not only showing an alert in the GUI but also processing and logging the event correctly in Suricata.

---

## Analysis

The lab showed that NetFlow and Traffic Graph worked well for identifying normal traffic and increased traffic in the environment. Baseline traffic could be observed first, and then compared against a heavier traffic pattern.

The IDS part required more investigation. Even though IDS was enabled and rules were loaded, the first standard tests did not generate alerts in the current configuration. This showed that IDS testing is not only about enabling rules, but also about making sure the traffic actually matches an active rule and reaches OPNsense in a way that can be inspected properly.

Creating a user defined ICMP rule provided a controlled method for verifying IDS functionality. This gave a clear result and confirmed that Suricata was working correctly in the environment.

---

## Validation Summary

| Validation Item | Result |
|----------------|--------|
| OPNsense LAN reachable | Success |
| Server reachable | Success |
| NetFlow enabled | Success |
| Traffic visible in Reporting > Traffic | Success |
| Top Talkers visible | Success |
| Increased traffic visible | Success |
| Initial IDS tests | No new alerts |
| User defined IDS rule | Success |
| Suricata log confirmation | Success |

---

## Conclusion

This lab showed that OPNsense can be used effectively in Hyper-V for traffic monitoring and IDS verification. NetFlow and Traffic Graph made it possible to observe both normal traffic and increased traffic on the LAN interface.

The initial IDS tests did not generate clear results, but further troubleshooting showed that IDS itself was functioning. By using a controlled user defined rule and verifying the result in the Suricata log, IDS functionality was confirmed successfully.

The lab provided a practical understanding of how traffic analysis and IDS verification work together, and why traffic path and rule matching are important in real testing scenarios.

---

## Key Takeaways

- NetFlow is useful for observing traffic patterns and changes
- Traffic Graph and Top Talkers help identify active hosts quickly
- IDS testing depends on both traffic path and active rule matching
- Same-subnet traffic may not always be ideal for firewall or IDS validation
- User defined rules are useful for controlled verification
- Suricata logs are important when confirming whether IDS is really working
