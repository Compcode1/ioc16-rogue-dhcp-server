# IOC 16 – Rogue DHCP Server (Unauthorized Configuration Injection)

This case study analyzes a rogue DHCP server scenario in which an attacker leverages a local foothold or physical access to intercept or redirect endpoint traffic by responding faster than the legitimate DHCP infrastructure. The attacker delivers falsified gateway, DNS, or subnet information, subverting routing behavior and enabling surveillance, traffic capture, or lateral movement within the network.

## Overview

DHCP (Dynamic Host Configuration Protocol) automates network configuration. In this attack, an unauthorized DHCP server impersonates a legitimate one, often responding first to broadcast DHCP DISCOVER requests. This allows the attacker to assign malicious settings (e.g., fake default gateway or DNS server) to nearby systems, rerouting traffic or isolating the target for further manipulation. These attacks are especially effective in flat or poorly segmented environments where switch-level protections are lacking.

## Key Concepts Covered

- The nature of DHCP trust and the risk of broadcast-based impersonation
- How rogue DHCP servers redirect traffic for surveillance or attack preparation
- Host-based telemetry for identifying configuration tampering
- Cross-layer forensic logic tracing background execution and persistence
- OSI layer involvement from Layer 3 routing redirection to Layer 7 spoofing tools

## Investigative Structure

This case uses the Host-Based IOC Triage Protocol to identify the rogue DHCP source and confirm its effects.

- **Telemetry Sources**:
  - EDR logs showing interface changes or unauthorized IP/gateway assignments
  - Windows Event ID 1001 (DHCP address assignment)
  - Wireshark detection of multiple DHCP OFFERs from unexpected sources

- **Host-Based Investigative Toolkit**:
  - **Windows Event Logs** – track assignment events and anomalies
  - **EDR Telemetry** – surface outbound traffic path changes and rogue services
  - **File System & Registry** – identify installed rogue DHCP tools or auto-start entries
  - **Volatile Memory Analysis** – detect `dhcpd`, `npf.sys`, or active spoofing utilities

- **Operating System Layers Involved**:
  - Layer 6: Network Communication – configuration redirection
  - Layer 3: Background Execution – rogue service persistence
  - Layer 2: Startup Infrastructure – autostart registration of DHCP tool

- **OSI Layers Affected**:
  - Layer 3: IP/gateway/DNS assignment
  - Layer 7: Rogue server emulation tools (Ettercap, Responder, Yersinia)

## Tools & Techniques Observed

- DHCP packet injection using tools like `Yersinia`, `Ettercap`, or `Responder`
- Malicious use of PowerShell or `netsh` to override gateway and DNS settings
- Memory-resident DHCP daemons or driver-level manipulation (e.g., `npf.sys`)
- Broadcast flooding to race legitimate DHCP servers

## Defender Response Framework

- Capture and inspect DHCP OFFER traffic using packet capture tools (e.g., Wireshark)
- Identify the MAC/IP of rogue DHCP responder via NetFlow or switch logs
- Disable unauthorized interfaces; isolate the rogue host from the network
- Enable port-based NAC or DHCP snooping to restrict broadcast privilege
- Flush affected system configurations and restore correct routing/gateway entries

## Strategic Takeaways

The rogue DHCP server is often used as a **positioning move**, not an end goal. By controlling gateway and DNS resolution, the attacker gains first-step influence over routing paths, sets up for man-in-the-middle attacks, and disables proper resolution of security controls. This is a low-skill, high-impact technique in networks that lack broadcast restrictions or endpoint telemetry correlation.

## Files

- `ioc16-rogue-dhcp-server.ipynb`: Full triage case study including attacker logic, OS and OSI layer analysis, and recommended defensive action

## License

This project is provided for defensive research and educational use. Attribution is encouraged.
