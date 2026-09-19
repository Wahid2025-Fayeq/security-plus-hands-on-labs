# Nmap Network Discovery Incident Report

## Report Summary

On September 19, 2026, an authorized network-discovery and service-analysis exercise was performed on a personal home network. Nmap identified eight active hosts and three open TCP ports on the Windows lab computer.

The investigation found no confirmed malicious activity.

## Environment

- Operating system: Windows
- Scanner: Nmap 7.99
- Packet-capture driver: Npcap 1.87
- Network range: 192.168.1.0/24
- Target system: Authorized personal Windows computer
- Network profile: Public

## Host-Discovery Findings

The host-discovery scan identified eight active devices. Recognized devices included:

- Network router
- Windows laptop
- Canon device
- iPad
- Roku
- Three devices with unidentified or randomized MAC addresses

An unidentified MAC vendor does not prove that a device is malicious. Additional verification through the router would be required.

## Service-Scan Findings

| Port    | State | Identified Service      |
| ------- | ----- | ----------------------- |
| 135/TCP | Open  | Microsoft RPC           |
| 139/TCP | Open  | NetBIOS Session Service |
| 445/TCP | Open  | Microsoft-DS/SMB        |

Ninety-seven additional common TCP ports were closed.

## Process Analysis

Port 135 was associated with `svchost.exe` and the following legitimate Windows services:

- RPC Endpoint Mapper (`RpcEptMapper`)
- Remote Procedure Call (`RpcSs`)

Ports 139 and 445 were associated with the Windows System process.

No executable running from an unusual directory was identified.

## Security Controls

The following controls were confirmed:

- Windows Firewall was enabled.
- The Public network profile was active.
- Inbound connections were blocked by default.
- Outbound connections were allowed.
- Remote firewall management was disabled.
- SMBv1 was disabled.

## Risk Assessment

**Severity:** Informational  
**Classification:** Expected system activity  
**Disposition:** Benign

The services were consistent with normal Windows networking. However, SMB and NetBIOS services should remain protected from untrusted networks.

The scan originated from the target computer itself. Therefore, an additional scan from another authorized device would be required to confirm whether the ports are reachable across the network.

## Recommendations

- Maintain the current firewall protections.
- Keep SMBv1 disabled.
- Disable unnecessary file-sharing functionality.
- Review unidentified devices using the router interface.
- Enable firewall logging if additional monitoring is required.
- Continue monitoring for unexpected ports or services.

## Evidence

- [Nmap laptop service scan](screenshots/01-nmap-laptop-service-scan.png)
- [Firewall and SMBv1 status](screenshots/02-firewall-and-smb1-status.png)

## Conclusion

The lab successfully demonstrated network discovery, service detection, process identification, and security-control validation. No confirmed malicious activity or immediately vulnerable service was discovered.
