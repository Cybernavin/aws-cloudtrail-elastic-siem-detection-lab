# Case 03 — S3 Data Exposure & Exfiltration Detection

## Summary

This lab simulates an attacker accessing and modifying sensitive files stored in an Amazon S3 bucket. AWS CloudTrail Data Events were enabled to capture object-level activity, and Elastic Security generated alerts for suspicious S3 operations.

## Environment

* **Cloud Provider:** AWS Free Tier (ap-southeast-1)
* **Log Source:** AWS CloudTrail Data Events (S3)
* **SIEM:** Elastic Security (Kibana)
* **Detection Rule:** S3 Data Exposure & Exfiltration Detection

## Attack Simulation

A bucket named `abhinav-sensitive-data-2026` was created with sample sensitive files:

* `employees.csv`
* `customer_database.json`
* `secret-api-keys.txt`

The following actions were performed:

1. Uploaded sensitive files (`PutObject`).
2. Downloaded files from the bucket (`GetObject`).
3. Deleted one file (`DeleteObject`).

CloudTrail recorded every object-level operation and forwarded the events to Elastic.

## Detection Logic

The detection rule monitored CloudTrail S3 events and generated high-severity alerts for:

* `GetObject`
* `PutObject`
* `DeleteObject`

The rule was limited to the sensitive S3 bucket used in this lab.

## Investigation Findings

Elastic Security detected multiple S3 object access events from the test IP address. The alerts included the event action, timestamp, source IP, bucket name, and affected object, making it easy to identify potential data exposure activity.

## MITRE ATT&CK Mapping

| Tactic                | Technique                                 |
| --------------------- | ----------------------------------------- |
| Collection (TA0009)   | Data from Cloud Storage Object (T1530)    |
| Exfiltration (TA0010) | Exfiltration to Cloud Storage (T1567.002) |

## Result

The lab successfully demonstrated how CloudTrail Data Events can detect unauthorized access and modification of sensitive S3 objects. Elastic Security generated alerts for simulated data exposure activities, providing visibility into potential cloud data exfiltration attempts.
