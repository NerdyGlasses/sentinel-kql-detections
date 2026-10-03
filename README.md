# Sentinel KQL Detections

A set of Microsoft Sentinel detection queries covering identity, Key Vault,
storage, and network misuse patterns, written in KQL (Kusto Query Language).

## What this is

Six detections across four categories, each documented with what it detects,
the data source it depends on, a MITRE ATT&CK mapping, and known false
positive scenarios - the same format a real detection engineering team
uses to document and hand off a rule.

Several of these pair directly with the
[azure-secure-baseline](../azure-secure-baseline) Terraform project: that
repo hardens a storage account, Key Vault, and NSG and wires up diagnostic
logging to a Log Analytics workspace; these queries are what would actually
watch those logs for misuse once deployed.

## Scope and honesty about what was tested

These queries were **written and reviewed for correct KQL syntax and schema
usage, but not run against a live Sentinel workspace or live log data** (no
active Azure subscription was available - see the azure-secure-baseline
README for why). Table and field names were checked against Microsoft's
published schema documentation for each log source.

If given access to a live environment, the next step would be: deploy the
Terraform baseline, let traffic/logs accumulate, run each query against
real data, and tune the thresholds (`ZScoreThreshold`, `SpeedThresholdKmh`,
etc.) based on what the environment's actual baseline looks like - these
starting values are reasonable defaults, not environment-tuned.

## Detections

| Detection | Category | Data Source | MITRE ATT&CK |
|---|---|---|---|
| [Impossible travel sign-in](identity/impossible-travel-signin.kql) | Identity | SigninLogs | T1078 |
| [Privileged role assignment](identity/privileged-role-assignment.kql) | Identity | AuditLogs | T1098 |
| [MFA fatigue / push bombing](identity/mfa-fatigue-pattern.kql) | Identity | SigninLogs | T1621 |
| [Anomalous Key Vault access](key-vault/anomalous-key-vault-access.kql) | Key Vault | AzureDiagnostics | T1552.001 / T1555 |
| [Mass blob download](storage/mass-blob-download.kql) | Storage | StorageBlobLogs | T1530 |
| [NSG opened to internet](network/nsg-opened-to-internet.kql) | Network | AzureActivity | T1562.007 |

Each `.kql` file is self-documenting - the header comment block explains
the detection, and the query itself has inline comments at non-obvious
steps.

## Why these six

Rather than write a large, shallow set of detections, this covers one
realistic scenario per category reflecting common real-world attack
patterns (credential compromise, privilege escalation, MFA bypass, secret
exfiltration, data exfiltration, and defense evasion via misconfiguration)
rather than being exhaustive. A production Sentinel deployment would have
many more, often pulled from Microsoft's built-in analytics rule templates
and Sentinel's own GitHub detection repository rather than hand-written
from scratch - these were written manually specifically to demonstrate
understanding of the underlying logic, not to replace that process.

## Structure

```
.
├── identity/
│   ├── impossible-travel-signin.kql
│   ├── privileged-role-assignment.kql
│   └── mfa-fatigue-pattern.kql
├── key-vault/
│   └── anomalous-key-vault-access.kql
├── storage/
│   └── mass-blob-download.kql
├── network/
│   └── nsg-opened-to-internet.kql
└── README.md
```
