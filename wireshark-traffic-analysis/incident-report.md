# Wireshark Network Traffic Analysis Report

## Report Summary

On September 19, 2026, controlled DNS and HTTPS traffic was generated and analyzed using Wireshark. The purpose was to practice packet analysis, protocol identification, network-connection tracing, and security-event triage.

The activity was authorized and performed on a personal computer and network. No malicious activity was identified.

## Environment

- Operating system: Windows 11
- Analysis tool: Wireshark 4.6.7
- Packet-capture driver: Npcap 1.87
- Capture interface: Wi-Fi
- Test domain: `example.com`
- Authorization: Personal lab environment

## Traffic Generation

The following commands generated the controlled network traffic:

```powershell
ipconfig /flushdns
nslookup example.com
curl.exe -I https://example.com
```

The DNS cache was cleared before requesting the domainâ€™s IP address. An HTTPS header request was then sent to the same domain.

## DNS Findings

Wireshark display filter:

```text
dns.id == 0x0004
```

| Field                     | Finding          |
| ------------------------- | ---------------- |
| Transaction ID            | `0x0004`         |
| Query type                | A record         |
| Requested domain          | `example.com`    |
| Returned IPv4 address     | `172.66.147.243` |
| Returned IPv4 address     | `104.20.23.154`  |
| Approximate response time | 6.4 milliseconds |

The DNS query and response used the same transaction ID, confirming that they belonged to the same lookup.

### Assessment

The DNS request was expected and successfully resolved the authorized test domain. No unexpected domain, failed lookup, or suspicious repetition was observed.

## TCP Findings

Wireshark display filter:

```text
frame.number >= 1779 && frame.number <= 1784
```

The following TCP three-way handshake was identified:

| Packet | TCP Flags | Description                                  |
| ------ | --------- | -------------------------------------------- |
| 1779   | SYN       | Client requested a connection                |
| 1780   | SYN, ACK  | Server accepted and acknowledged the request |
| 1781   | ACK       | Client confirmed the connection              |

The client used temporary port `29558` to connect to destination port `443`, which is commonly used for HTTPS.

The TCP handshake completed in approximately 12.2 milliseconds.

### Assessment

The handshake completed normally. No repeated SYN attempts, reset connection, or unusual destination port was observed.

## TLS Findings

After the TCP connection was established, TLS 1.3 negotiation occurred:

| Packet | TLS Event    | Description                  |
| ------ | ------------ | ---------------------------- |
| 1782   | Client Hello | Client proposed TLS settings |
| 1784   | Server Hello | Server selected TLS settings |

The Client Hello included:

```text
SNI=example.com
```

The SNI field revealed the requested domain, while the later HTTPS application content remained encrypted.

### Assessment

The TLS negotiation was consistent with normal encrypted web traffic. The connection used TLS 1.3 and destination port 443. No suspicious certificate warning or failed TLS negotiation was observed in the analyzed sequence.

## Event Sequence

```text
DNS query
â†’ DNS response
â†’ TCP SYN
â†’ TCP SYN-ACK
â†’ TCP ACK
â†’ TLS Client Hello
â†’ TLS Server Hello
â†’ Encrypted HTTPS traffic
```

## Security Significance

A SOC analyst can use DNS, TCP, and TLS metadata to investigate network activity even when application content is encrypted.

Potential warning signs could include:

- Queries for suspicious or newly registered domains
- Repeated DNS failures
- Connections to unusual external addresses
- Unexpected destination ports
- Large numbers of failed TCP connections
- Abnormal or outdated TLS versions
- Unexpected SNI values
- Repeated encrypted connections at unusual intervals

None of these indicators were confirmed in this controlled activity.

## Classification

- Severity: Informational
- Classification: Benign
- Disposition: Authorized lab activity
- Escalation required: No

## Evidence

- [DNS query and response](screenshots/01-dns-query-response.png)
- [TCP and TLS handshake](screenshots/02-tcp-tls-handshake.png)

The raw packet capture was retained privately and excluded from GitHub because it may contain sensitive network identifiers.

## Recommendations

- Use encrypted protocols such as HTTPS.
- Keep Wireshark and Npcap updated.
- Monitor DNS requests for suspicious domains.
- Investigate repeated or abnormal connection attempts.
- Protect packet captures as potentially sensitive evidence.
- Redact IP addresses, MAC addresses, and interface identifiers before publishing screenshots.

## Conclusion

Wireshark successfully captured the controlled DNS lookup, TCP three-way handshake, and TLS 1.3 negotiation. The activity followed the expected network-communication sequence and was classified as benign.

