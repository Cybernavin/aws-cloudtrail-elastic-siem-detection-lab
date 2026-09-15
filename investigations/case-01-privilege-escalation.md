# Case 01 — IAM Privilege Escalation Investigation

## Incident Summary

This lab demonstrates how **Elastic Security** detects an AWS IAM privilege escalation event using **AWS CloudTrail** logs.

The alert was triggered when the `AdministratorAccess` policy was attached to the IAM user **developer-user** through the `AttachUserPolicy` API.

| **Field**    | **Value**                    |
| ------------ | ---------------------------- |
| Project      | IAM Privilege Escalation     |
| Severity     | High                         |
| MITRE ATT&CK | T1098 – Account Manipulation |
| Status       | Successfully Detected        |

---

## What Happened?

An AWS identity attached the **AdministratorAccess** managed policy to `developer-user`. This gives the user full administrative permissions, making it a high-risk privilege escalation event.

---

## Detection Workflow

1. `AttachUserPolicy` event generated in AWS IAM.
2. CloudTrail recorded the event.
3. Logs were sent through **S3 → SQS → Elastic Agent**.
4. Elasticsearch indexed the event.
5. Elastic Security matched the KQL rule and generated a **High Severity** alert.

---

## Detection Rule

```kql id="gg2h9h"
event.action:"AttachUserPolicy"
and aws.cloudtrail.flattened.request_parameters:*AdministratorAccess*
```

**Rule Configuration**

* Runs every: **1 minute**
* Additional look-back: **120 minutes**
* Severity: **High**
* Risk Score: **80**

---

## Key Evidence

The investigation focused on these CloudTrail fields:

* `event.action`
* `@timestamp`
* `source.ip`
* `user.name`
* `aws.cloudtrail.user_identity.arn`
* `aws.cloudtrail.flattened.request_parameters`

These fields identify who performed the action, which user received the policy, and when it happened.

---

## MITRE ATT&CK Mapping

| **Tactic**           | **Technique**                    |
| -------------------- | -------------------------------- |
| Privilege Escalation | **T1098 – Account Manipulation** |
| Persistence          | **T1098 – Account Manipulation** |

---

## Screenshots

Screenshots for this case are available in:

```case-01-screenshots/
```

* CloudTrail event in Discover
* Detection rule
* Alert generated in Elastic Security
* Rule execution results
