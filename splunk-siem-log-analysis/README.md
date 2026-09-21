# Splunk SIEM Log Analysis Lab

## Overview

This lab demonstrates how Splunk Enterprise can ingest, search, analyze, and visualize Windows security and Sysmon event data. The investigation focused on failed authentication attempts and process-creation activity from an authorized Windows lab computer.

## Objectives

- Install and verify Splunk Enterprise.
- Import Windows Security and Sysmon CSV data.
- Search events using Splunk Processing Language (SPL).
- Analyze Windows Event ID 4625 authentication failures.
- Analyze Sysmon Event ID 1 process-creation activity.
- Examine parent-child process relationships.
- Visualize security findings.
- Protect sensitive log data before publishing the project.

## Tools Used

- Windows 11
- Splunk Enterprise 10.4.3
- Microsoft Sysmon 15.22
- Windows Event Viewer
- PowerShell
- Visual Studio Code

## Data Ingestion

Two CSV datasets were imported into the Splunk index named `security_lab`:

| Data source            | Events | Description                                   |
| ---------------------- | -----: | --------------------------------------------- |
| `failed-logins.csv`    |     20 | Windows Event ID 4625 authentication failures |
| `sysmon-processes.csv` |      4 | Sysmon Event ID 1 process-creation events     |

The imported data used the following settings:

- Index: `security_lab`
- Source type: `csv`
- Host label: `windows-lab`

## Failed-Login Analysis

The following SPL query extracted authentication sub-status codes and translated them into readable failure categories:

```spl
index=security_lab source="*failed-logins.csv"
| rex field=Message "Sub Status:\s+(?<SubStatus>0x[0-9A-Fa-f]+)"
| eval FailureType=case(
    SubStatus=="0xC0000064", "Unknown username",
    SubStatus=="0xC000006A", "Incorrect password",
    SubStatus=="0xC0000380", "Incorrect PIN credential",
    true(), "Other"
)
| stats count AS Attempts by FailureType
| sort - Attempts
```

### Findings

| Failure type             | Sub-status   | Attempts |
| ------------------------ | ------------ | -------: |
| Unknown username         | `0xC0000064` |        9 |
| Incorrect password       | `0xC000006A` |        9 |
| Incorrect PIN credential | `0xC0000380` |        2 |

All 20 records were Windows Event ID `4625`, which represents a failed logon attempt.

![Failed-login analysis](screenshots/01-failed-login-analysis.png)

## Sysmon Process Analysis

The Sysmon dataset contained four Event ID `1` process-creation events.

The following SPL query extracted the executable and parent executable from each event:

```spl
index=security_lab source="*sysmon-processes.csv"
| rex field=Message "(?m)^Image:\s+(?<Image>[^\r\n]+)"
| rex field=Message "(?m)^ParentImage:\s+(?<ParentImage>[^\r\n]+)"
| eval Child=replace(Image,"^.*\\\\","")
| eval Parent=replace(ParentImage,"^.*\\\\","")
| stats count AS Events by Parent Child
| sort Parent Child
```

### Findings

| Parent process   | Child process | Events | Interpretation                                 |
| ---------------- | ------------- | -----: | ---------------------------------------------- |
| `powershell.exe` | `Notepad.exe` |      1 | PowerShell launched Notepad                    |
| `Notepad.exe`    | `Notepad.exe` |      1 | Modern Notepad started its application process |
| `powershell.exe` | `cmd.exe`     |      1 | PowerShell launched Command Prompt             |
| `cmd.exe`        | `whoami.exe`  |      1 | Command Prompt performed account discovery     |

![Sysmon process-chain analysis](screenshots/02-sysmon-process-chain-analysis.png)

## Security Analysis

The failed-login records demonstrated how a SIEM can group authentication failures by cause. In a production environment, repeated failures involving many usernames or a single source could indicate password guessing, password spraying, credential misuse, or a misconfigured application.

The process events were authorized and benign. However, the `cmd.exe → whoami.exe` relationship is security-relevant because attackers may use `whoami` after gaining access to identify the current account.

A single event does not prove malicious activity. A SOC analyst should correlate the event with its user, device, command line, parent process, network activity, timing, and related security alerts.

## Classification

- Severity: Informational
- Classification: Benign
- Disposition: Authorized lab activity
- Escalation required: No

## Privacy Protection

Raw CSV files may contain usernames, computer names, security identifiers, and executable paths. They were excluded from Git using:

```gitignore
*.csv
```

Only aggregated and sanitized Splunk results are included in the public evidence.

## Incident Report

See the complete [Splunk SIEM Log Analysis Report](incident-report.md).

## Skills Demonstrated

- Splunk Enterprise administration
- Security-log ingestion
- Splunk Processing Language
- Field extraction using `rex`
- Windows Event ID 4625 analysis
- Sysmon Event ID 1 analysis
- Parent-child process investigation
- Security-event visualization
- SIEM alert triage
- Evidence handling and privacy protection
- Technical documentation

## Ethical Use

All events were generated and analyzed in an authorized personal lab environment. No unauthorized systems or data were accessed.
