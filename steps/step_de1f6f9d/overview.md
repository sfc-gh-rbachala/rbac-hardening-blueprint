<!-- Author: Richie Bachala (richie.bachala@snowflake.com) -->
In this step, you'll create a set of monitoring views in a dedicated RBAC_MONITORING database. These views provide ongoing visibility into role membership, privilege changes, direct user grants (RBAC bypasses), role hierarchy depth, and user activity status.

**Account Context:** Execute from the target account with SYSADMIN (view creation) and SECURITYADMIN (grants).

## Why is this important?

RBAC hardening is not a one-time activity. Without continuous monitoring:
- **Configuration drift** — New direct grants or PUBLIC grants accumulate over time
- **Privilege creep** — Users accumulate roles beyond what they need
- **Dormant accounts** — Inactive users retain access, increasing attack surface
- **Compliance gaps** — Auditors expect evidence of ongoing access review

These monitoring views provide the foundation for alerts (Step 4.2) and access review reports (Step 4.3).

## Prerequisites

- Tasks 1-3 completed (assessment, hardening, provisioning)
- A designated warehouse for running monitoring queries
- SYSADMIN role for creating the monitoring database and views

## Key Concepts

**Monitoring Views**
| View | Purpose | Data Source |
|------|---------|-------------|
| V_ROLE_MEMBERSHIP | Current role assignments | GRANTS_TO_USERS |
| V_PRIVILEGE_CHANGES | Grant/revoke history | GRANTS_TO_ROLES |
| V_DIRECT_USER_GRANTS | RBAC bypass detection | GRANTS_TO_USERS |
| V_ROLE_HIERARCHY_DEPTH | Role chain analysis | GRANTS_TO_ROLES (recursive) |
| V_USER_ACTIVITY | Login/dormancy tracking | USERS |

**Health Indicators**
- `V_DIRECT_USER_GRANTS` should return **zero rows** (no bypasses)
- `V_ROLE_HIERARCHY_DEPTH` should show all roles as **HEALTHY** (depth ≤ 4)
- `V_USER_ACTIVITY` should have no **DORMANT** users with active roles

## Best Practices

- Schedule monitoring queries to run on a regular cadence (daily or weekly)
- Use a dedicated, auto-suspend warehouse for monitoring to control costs
- Grant SELECT on monitoring views to your security operations role
- Integrate monitoring view outputs with your SIEM or alerting platform

## How to Test

1. Query each view and verify it returns expected results
2. Confirm V_DIRECT_USER_GRANTS returns zero rows (if Step 2.3 was completed)
3. Check V_ROLE_HIERARCHY_DEPTH for any CRITICAL or WARNING roles
4. Review V_USER_ACTIVITY for dormant or never-logged-in users

## More Information

* [Account Usage Views](https://docs.snowflake.com/en/sql-reference/account-usage) — Data sources
* [GRANTS_TO_ROLES](https://docs.snowflake.com/en/sql-reference/account-usage/grants_to_roles) — Privilege tracking
* [GRANTS_TO_USERS](https://docs.snowflake.com/en/sql-reference/account-usage/grants_to_users) — User access tracking
