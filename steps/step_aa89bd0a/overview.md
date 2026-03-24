<!-- Author: Richie Bachala (richie.bachala@snowflake.com) -->
In this step, you'll generate diagnostic queries that detect stale access — unused roles, dormant users with active privileges, and users who have never logged in but hold role grants. Stale access is a security risk because it provides attack surface without business value.

**Account Context:** Execute these queries from the target account with ACCOUNTADMIN role.

## Why is this important?

Stale access accumulates over time as employees change roles, leave the organization, or projects are decommissioned. Risks include:
- **Orphaned credentials** — Dormant users may have credentials that are compromised without detection
- **Unused roles** — Roles with no active users consuming them indicate RBAC drift
- **Compliance violations** — Most frameworks (SOC 2, HIPAA) require periodic access review and removal of stale grants

## Prerequisites

- Account accessible with ACCOUNTADMIN role
- SNOWFLAKE database IMPORTED PRIVILEGES granted
- LOGIN_HISTORY data available (up to 365 days)

## Key Concepts

**Dormant Users**
Users who have not logged in for 90+ days but still have active role grants. These accounts should be reviewed for deactivation or privilege removal.

**Unused Roles**
Roles that exist in the hierarchy but are not granted to any user, or are only granted to users who haven't logged in recently.

**Last Login Analysis**
Snowflake's LOGIN_HISTORY view provides login records for up to 365 days. Users are bucketed into:
- **Active** (<30 days since last login)
- **Stale** (30-90 days)
- **Dormant** (>90 days)
- **Never** (no login record)

## Best Practices

- Disable dormant users (`ALTER USER SET DISABLED = TRUE`) rather than dropping them immediately
- Revoke role grants from dormant users before disabling
- Keep disabled users for 30-90 day retention period, then drop
- Run stale access detection monthly

## How to Test

1. Run the dormant users query — verify results against your HR/identity records
2. Cross-reference unused roles with the role hierarchy audit
3. Confirm "never logged in" users are not pending onboarding

## More Information

* [LOGIN_HISTORY](https://docs.snowflake.com/en/sql-reference/account-usage/login_history) — User login records
* [ALTER USER](https://docs.snowflake.com/en/sql-reference/sql/alter-user) — Disabling users
* [QUERY_HISTORY](https://docs.snowflake.com/en/sql-reference/account-usage/query_history) — Query activity
