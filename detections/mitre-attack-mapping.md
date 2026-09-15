# MITRE ATT&CK Mapping — IAM Privilege Escalation

This document explains how the **IAM Privilege Escalation** detection rule maps to the MITRE ATT&CK framework and why this activity is considered a high-risk security event.

## Project Overview

| Field              | Value                                 |
| ------------------ | ------------------------------------- |
| **Project**        | IAM Privilege Escalation              |
| **Platform**       | AWS CloudTrail + Elastic Security     |
| **Detection Rule** | AdministratorAccess Attached          |
| **Log Source**     | AWS CloudTrail                        |
| **Severity**       | High                                  |
| **Risk Score**     | 80                                    |
| **Schedule**       | Every 1 minute (120-minute look-back) |

---

## MITRE ATT&CK Mapping

| Tactic               | Technique            | ID        |
| -------------------- | -------------------- | --------- |
| Privilege Escalation | Account Manipulation | **T1098** |
| Persistence          | Account Manipulation | **T1098** |

### Why T1098?

The detection monitors the `AttachUserPolicy` API call and triggers when the AWS managed `AdministratorAccess` policy is attached to an IAM user. Granting this policy provides full administrative permissions, making it a common privilege escalation technique.

---

## Detection Logic

**Elastic KQL**

```kql
event.action:"AttachUserPolicy"
and aws.cloudtrail.flattened.request_parameters:*AdministratorAccess*
```

This rule generates a **High Severity** alert whenever an IAM user receives the `AdministratorAccess` policy.

---

## Investigation Checklist

When this alert is triggered, verify:

* Who attached the policy?
* Which IAM user received AdministratorAccess?
* What was the source IP address?
* Was the action expected or authorized?
* Were any other IAM changes made after this event?

Useful CloudTrail fields:

* `event.action`
* `@timestamp`
* `source.ip`
* `user.name`
* `aws.cloudtrail.user_identity.arn`
* `aws.cloudtrail.flattened.request_parameters`

---

## Response Actions

If the activity is unauthorized:

1. Remove the `AdministratorAccess` policy from the affected IAM user.
2. Disable or rotate the compromised access key or credentials.
3. Review recent CloudTrail events for additional IAM changes.
4. Investigate related actions such as `CreateAccessKey`, `PutUserPolicy`, or `AttachRolePolicy`.

---

## Detection Flow

AWS IAM → CloudTrail → S3 → SQS → Elastic Agent → Elasticsearch → Kibana Security → **High Severity Alert**
