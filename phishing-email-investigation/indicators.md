# Phishing Indicators

| Type        | Indicator                                                        | Assessment                            |
| ----------- | ---------------------------------------------------------------- | ------------------------------------- |
| Sender      | alerts@micros0ft-security.example                                | Lookalike domain uses 0 instead of o  |
| Reply-To    | verification@account-check.example                               | Does not match the sender domain      |
| Return-Path | bounce@mailer.example                                            | Does not match the sender domain      |
| Source IP   | 203.0.113.77                                                     | Simulated originating IP              |
| URL         | hxxps://login-security-review[.]example/verify                   | Defanged credential-verification link |
| SPF         | Fail                                                             | Sending server was not authorized     |
| DKIM        | None                                                             | No valid message signature            |
| DMARC       | Fail                                                             | Sender-domain alignment failed        |
| SHA-256     | 90F54155C25AF38273E27023594389295447C3F283ED32E8F5E5B67B35AF8F09 | Evidence-file fingerprint             |

## Social-Engineering Indicators

- Urgent subject line
- Threat of account suspension
- Request to verify a password
- Thirty-minute deadline
- Impersonation of Microsoft
- Link domain does not match the claimed organization

## Initial Classification

- Category: Credential-phishing attempt
- Severity: High
- Confidence: High
- Disposition: Simulated true positive
