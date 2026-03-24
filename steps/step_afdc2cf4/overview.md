<!-- Author: Richie Bachala (richie.bachala@snowflake.com) -->
In this step, you'll generate a comprehensive access review report that covers all aspects of your RBAC posture. This report should be run on a regular cadence and reviewed by your security team to maintain compliance and detect configuration drift.

**Account Context:** Execute from the target account with SECURITYADMIN role.

## Why is this important?

Access reviews are a core requirement for security compliance frameworks:
- **SOC 2** — Requires periodic access reviews for logical access controls
- **HIPAA** — Requires regular review of access to PHI
- **PCI-DSS** — Requires quarterly access reviews for cardholder data environments
- **SOX** — Requires evidence of access control effectiveness

Beyond compliance, regular reviews catch:
- Privilege creep from role accumulation
- Dormant accounts that increase attack surface
- Shadow access from direct user grants
- Overprivileged service accounts

## Prerequisites

- Step 4.1 (RBAC Monitoring Views) completed — report queries these views
- Step 4.2 (Alerts) recommended but not required
- Monitoring warehouse provisioned
- SECURITYADMIN role access

## Key Concepts

**Report Sections**
| # | Section | What It Checks |
|---|---------|---------------|
| 1 | Executive Summary | Overall user counts and health metrics |
| 2 | ACCOUNTADMIN Holders | Verify against authorized break-glass list |
| 3 | RBAC Bypasses | Direct user grants (should be zero) |
| 4 | Role Hierarchy Health | Excessive role chain depth |
| 5 | Dormant Users | Inactive users with active roles |
| 6 | Recent Changes | Privilege modifications since last review |
| 7 | PUBLIC Role Grants | Grants to PUBLIC (should be minimal) |
| 8 | MFA Status | Users without MFA on privileged roles |

**Review Frequency**
| Cadence | Lookback Period | Best For |
|---------|----------------|----------|
| Weekly | 7 days | High-security environments, SOX |
| Monthly | 30 days | Standard compliance requirements |
| Quarterly | 90 days | Lower-risk environments |

## Best Practices

- Assign a specific owner for each report section
- Track remediation actions (disable dormant users, revoke bypasses)
- Archive reports for audit evidence
- Escalate critical findings (unauthorized ACCOUNTADMIN, RBAC bypasses)
- Trend metrics over time to identify improvement or degradation

## How to Test

1. Run the full report query set
2. Verify each section returns data (or the expected empty set)
3. Cross-reference Section 2 with your authorized ACCOUNTADMIN list
4. Confirm Section 3 returns zero rows (no RBAC bypasses)

## More Information

* [Access Control](https://docs.snowflake.com/en/user-guide/security-access-control) — Security overview
* [Account Usage](https://docs.snowflake.com/en/sql-reference/account-usage) — Audit data sources
