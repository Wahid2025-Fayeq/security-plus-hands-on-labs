# Phishing Email Investigation Report

## Report Summary

On October 8, 2026, a simulated suspicious email was investigated in a controlled personal lab environment.

The message impersonated Microsoft Security, threatened account suspension, and instructed the recipient to verify a password through a suspicious link.

Analysis of the message headers, authentication results, sender domains, source information, and message content confirmed that the email represented a simulated credential-phishing attempt.

No user clicked the link, no credentials were submitted, and no system compromise occurred.

## Environment

- Operating system: Windows
- Analysis tools: PowerShell and Visual Studio Code
- Evidence type: Simulated `.eml` email file
- Investigation date: October 8, 2026
- Authorization: Personal lab environment

## Incident Classification

| Field                | Assessment              |
| -------------------- | ----------------------- |
| Category             | Credential phishing     |
| Severity             | High                    |
| Confidence           | High                    |
| Disposition          | Simulated true positive |
| User interaction     | None                    |
| Credentials exposed  | No                      |
| Confirmed compromise | No                      |
| Escalation required  | No for this simulation  |

## Email Summary

| Field        | Value                                             |
| ------------ | ------------------------------------------------- |
| Display name | Microsoft Security                                |
| Sender       | `alerts@micros0ft-security.example`               |
| Reply-To     | `verification@account-check.example`              |
| Return-Path  | `bounce@mailer.example`                           |
| Subject      | `URGENT: Your Microsoft account will be disabled` |
| Source IP    | `203.0.113.77`                                    |
| URL          | `hxxps://login-security-review[.]example/verify`  |

All domains and IP addresses used in this lab were reserved for documentation and did not represent real malicious infrastructure.

## Evidence Preservation

The original simulated email was saved as:

```text
sample-phishing-email.eml
```

A SHA-256 hash was calculated to establish an evidence fingerprint:

```text
90F54155C25AF38273E27023594389295447C3F283ED32E8F5E5B67B35AF8F09
```

Command used:

```powershell
Get-FileHash `
  -Path ".\sample-phishing-email.eml" `
  -Algorithm SHA256
```

If the file changes, its SHA-256 value should also change.

## Header Analysis

Important headers were extracted using:

```powershell
Select-String `
  -Path ".\sample-phishing-email.eml" `
  -Pattern '^(From|Reply-To|Return-Path|Subject|Authentication-Results|X-Originating-IP):|^\s+(spf|dkim|dmarc)='
```

### Sender Findings

The sender address used:

```text
alerts@micros0ft-security.example
```

The domain replaced the letter `o` in `microsoft` with the number `0`. This lookalike-domain technique was intended to imitate a trusted organization.

The sender, Reply-To, and Return-Path domains did not match:

```text
From:        micros0ft-security.example
Reply-To:    account-check.example
Return-Path: mailer.example
```

This mismatch indicated that responses and delivery messages would be directed to domains different from the displayed sender.

## Authentication Findings

| Authentication control | Result | Assessment                             |
| ---------------------- | ------ | -------------------------------------- |
| SPF                    | Fail   | Sending source was not authorized      |
| DKIM                   | None   | No valid message signature was present |
| DMARC                  | Fail   | Visible sender-domain alignment failed |

### SPF

The SPF result was `fail`. This indicated that the sending server was not authorized to send email for the envelope-sender domain.

### DKIM

The DKIM result was `none`. The message did not contain a verifiable domain-level digital signature.

### DMARC

The DMARC result was `fail`. The visible sender domain did not align with an authenticated SPF or DKIM domain.

The combination of SPF failure, missing DKIM, and DMARC failure strongly supported the phishing classification.

## Social-Engineering Findings

The message included several psychological manipulation techniques:

- Urgent subject line
- Threat of account suspension
- Request to verify a password
- Thirty-minute deadline
- Impersonation of Microsoft
- Pressure to act without verification
- Link domain unrelated to the claimed organization

PowerShell was used to find this language:

```powershell
Select-String `
  -Path ".\sample-phishing-email.eml" `
  -Pattern 'urgent|disabled|verify|30 minutes|hxxps?://'
```

## URL Analysis

The message contained the following defanged URL:

```text
hxxps://login-security-review[.]example/verify
```

The URL was considered suspicious because:

- It did not use a legitimate Microsoft domain.
- It included account-verification language.
- It was paired with a request to verify a password.
- It was combined with urgency and a threat of account suspension.

The URL was not opened during the investigation.

## Indicators of Compromise

| Type         | Indicator                                                          |
| ------------ | ------------------------------------------------------------------ |
| Sender       | `alerts@micros0ft-security.example`                                |
| Reply-To     | `verification@account-check.example`                               |
| Return-Path  | `bounce@mailer.example`                                            |
| Source IP    | `203.0.113.77`                                                     |
| URL          | `hxxps://login-security-review[.]example/verify`                   |
| SPF result   | `fail`                                                             |
| DKIM result  | `none`                                                             |
| DMARC result | `fail`                                                             |
| File SHA-256 | `90F54155C25AF38273E27023594389295447C3F283ED32E8F5E5B67B35AF8F09` |

### Evidence

![Phishing indicators](screenshots/01-phishing-indicators.png)

![Simulated phishing email](screenshots/02-simulated-phishing-email.png)

## Recommended Containment Actions

If this were a real production incident, the security team should:

- Quarantine the email from all mailboxes.
- Block the sender, Reply-To, and Return-Path domains.
- Block the URL through email, DNS, proxy, and endpoint-security controls.
- Search email logs for other recipients.
- Search SIEM and endpoint data for matching indicators.
- Determine whether any user clicked the link.
- Preserve the original email and complete headers.
- Notify affected users and the security team.

## Recommended Recovery Actions

If a user entered credentials, the security team should:

- Reset the affected password immediately.
- Revoke active sessions and authentication tokens.
- Review account sign-in history.
- Check for newly registered MFA methods.
- Review mailbox-forwarding and inbox rules.
- Examine the endpoint for suspicious processes or downloads.
- Monitor the account for additional unauthorized activity.

## Preventive Recommendations

- Enforce multifactor authentication.
- Configure SPF, DKIM, and DMARC.
- Use secure email-gateway filtering.
- Provide phishing-awareness training.
- Display external-sender warnings.
- Use URL rewriting and time-of-click protection.
- Alert on lookalike domains and authentication failures.
- Encourage users to report suspicious messages.

## Final Assessment

The simulated message was classified as a High-severity credential-phishing attempt with High confidence.

The strongest indicators were:

- Lookalike sender domain
- Mismatched sender and reply domains
- SPF failure
- Missing DKIM signature
- DMARC failure
- Credential-verification request
- Urgent deadline and threat
- Suspicious link domain

## Conclusion

The investigation demonstrated how email headers, authentication controls, message content, source information, URLs, and evidence hashes can be combined to classify a phishing email.

The message was identified before any user interaction occurred, and no compromise was present in the controlled lab.
