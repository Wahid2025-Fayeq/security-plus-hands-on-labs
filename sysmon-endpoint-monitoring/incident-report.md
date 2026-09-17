# Sysmon Endpoint Activity Analysis Report

## Report Summary

On September 17, 2026, two controlled process-creation activities were generated and analyzed using Microsoft Sysmon. The purpose was to practice endpoint monitoring, process-tree analysis, and security-event triage.

## Environment

- Operating system: Windows 11
- Monitoring tool: Microsoft Sysmon 15.22
- Log source: Microsoft-Windows-Sysmon/Operational
- Event investigated: Event ID 1 — Process Create
- Authorization: Personal lab computer

## Activity 1: Notepad Process Creation

### Observed Process Chain

powershell.exe → notepad.exe

### Evidence

- Time observed: September 17, 2026, 11:34:50 AM
- Image: Notepad.exe
- Parent image: powershell.exe
- Command line: C:\Windows\System32\notepad.exe
- Integrity level: High
- SHA-256: 03745E9E21684C0E6D9FDC50D91CAA0935F4BAF50F626C11987AB7D6991D58D1

### Assessment

The activity was expected and authorized. PowerShell was intentionally used to launch Notepad. No additional suspicious behavior was observed.

### Classification

Benign — authorized lab activity

## Activity 2: Account Discovery

### Observed Process Chain

powershell.exe → cmd.exe → whoami.exe

### Evidence

- Time observed: September 17, 2026, 11:54:29 AM
- Image: C:\Windows\System32\whoami.exe
- Command line: whoami
- Parent image: C:\Windows\System32\cmd.exe
- Parent command line: cmd.exe /c whoami
- Integrity level: High
- Company: Microsoft Corporation
- SHA-256: 23240EF9F8B0A9A324110B1C2331DE31DC1B0E08F5359CB707E51A939AF56CD3

### Assessment

The command was intentionally executed as part of the lab. Although `whoami` is a legitimate Windows utility, attackers may use it to identify the account available on a compromised system.

This event would become more suspicious if it followed a phishing document, encoded PowerShell command, unexpected remote login, or malware execution.

### Classification

Benign but security-relevant — authorized discovery simulation

## Recommended Investigation Actions

If similar activity appeared unexpectedly in a production environment, an analyst should:

- Review the complete parent-child process chain.
- Examine commands executed immediately before and afterward.
- Verify the user and device involved.
- Check the executable's signature and hash.
- Review network connections and DNS queries.
- Search for file, Registry, service, or scheduled-task changes.
- Correlate the activity with SIEM and EDR alerts.

## Conclusion

Sysmon successfully captured both controlled activities. The investigation demonstrated that individual events provide evidence but require additional context and correlation before being classified as malicious.
