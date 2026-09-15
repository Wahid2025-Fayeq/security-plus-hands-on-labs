# Failed Windows Login Incident Report

## Incident Summary

On September 15, 2026, three controlled failed-login attempts were
generated on an authorized Windows 11 computer. Windows Event Viewer
recorded the activity as Security Event ID 4625.

## Evidence

- Event ID: 4625
- Target account: FakeLabUser
- Logon type: 2 — Interactive login
- Failure reason: Unknown username or bad password
- Status: 0xC000006D
- Sub-status: 0xC0000064
- Source address: ::1 — Local IPv6 loopback
- Authentication package: Negotiate

## Analysis

The sub-status code indicates that the requested account did not exist.
The source address shows that the request originated from the local
computer. This activity was generated intentionally using the Windows
runas command in an authorized security lab.

In a production environment, repeated Event ID 4625 records could
indicate password guessing, brute-force activity, user error, or an
incorrectly configured service.

## Recommended Controls

- Monitor repeated failed-login events.
- Enable multifactor authentication.
- Configure an appropriate account-lockout policy.
- Use strong password requirements.
- Investigate failures followed by successful logins.
- Create SIEM alerts for unusual authentication activity.

## Conclusion

The investigation successfully identified and interpreted a failed
Windows authentication attempt using native Windows Security logs.
