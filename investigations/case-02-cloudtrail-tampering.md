# Case 02 — CloudTrail Tampering Detection

## Summary

A CloudTrail tampering attempt was simulated by stopping logging, starting it again, and updating the trail configuration. Elastic SIEM detected all three activities and generated alerts.

## Timeline

| Time | Event |
|------|------|
| 07:51 | `StopLogging` |
| 07:53 | `StartLogging` |
| 08:01 | `UpdateTrail` |

## Detection Rule

**Rule:** CloudTrail Tampering Detection

**KQL**

```kql
event.action:("StopLogging" or "StartLogging" or "UpdateTrail")
```

## MITRE ATT&CK

- **Tactic:** Defense Evasion (TA0005)
- **Technique:** Impair Defenses (T1562)

## Investigation

The events originated from the same AWS account and source IP. No additional suspicious IAM activity was observed during this test, indicating this was a controlled attack simulation.

## Outcome

- Elastic SIEM created alerts for CloudTrail tampering events.
- The alerts included the event action, timestamp, source IP, and AWS user details.

## Evidence

Screenshots are available in the `case-02-screenshots/` folder.