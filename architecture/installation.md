# 🚀 Installation Guide

This lab was built on **Windows 10** using **AWS Free Tier** and **Elastic Cloud**. The goal is to collect AWS CloudTrail logs, ingest them into Elastic SIEM, and generate security alerts for IAM privilege escalation events.

## Prerequisites

- AWS Free Tier account
- Elastic Cloud deployment (Security enabled)
- Elastic Agent installed on Windows
- PowerShell (Run as Administrator)

---

## 1. Create AWS Resources

Create the following resources in AWS:

| Resource | Purpose |
|----------|---------|
| CloudTrail | Collect IAM management events |
| S3 Bucket | Store CloudTrail logs |
| SQS Queue | Send S3 notifications to Elastic |
| IAM Users | Simulate admin, developer, attacker, and Elastic integration |

---

## 2. Configure CloudTrail

- Create a **multi-region** CloudTrail.
- Enable **Management Events** (Read + Write).
- Set the destination to your S3 bucket.
- Enable log file validation.

---

## 3. Configure Elastic Cloud

In **Kibana → Integrations**, install the **AWS CloudTrail** integration.

Provide:

- AWS Region
- SQS Queue URL
- Access Key & Secret Key for the `elastic-cloudtrail-user`

Wait a few minutes for the first logs to arrive.

---

## 4. Install Elastic Agent

Install the agent using the Fleet enrollment command from Kibana.

Verify it's connected:

```powershell
elastic-agent status
```

Expected output:

```text
Fleet: HEALTHY (Connected)
Elastic Agent: HEALTHY (Running)
```

---

## 5. Verify CloudTrail Logs

Go to **Discover** and run:

```kql
data_stream.dataset:"aws.cloudtrail"
```

You should see CloudTrail events being ingested into Elastic.

Useful queries:

```kql
event.category:iam
```

```kql
event.action:"AttachUserPolicy"
```

---

## 6. Create the Detection Rule

Create a **Custom Query** rule with:

- **Index Pattern:** `logs-*`
- **Runs Every:** 1 minute
- **Look-back:** 30 minutes

Query:

```kql
event.action:"AttachUserPolicy"
```

Set **Severity: High** and enable the rule.

---

## 7. Test the Detection

Login as **SOC-Admin** in AWS.

Attach the `AdministratorAccess` policy to `developer-user`.

After about a minute, the rule should generate an alert in **Security → Rules → Alerts**.

---


## ✅ Expected Result

- CloudTrail logs are ingested into Elastic.
- `AttachUserPolicy` events appear in Discover.
- Elastic SIEM detects the activity.
- A **High Severity** alert is generated for IAM privilege escalation.