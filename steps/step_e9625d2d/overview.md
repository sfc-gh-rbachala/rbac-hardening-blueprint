<!-- Author: Richie Bachala (richie.bachala@snowflake.com) -->
In this step, you'll generate diagnostic queries that discover privilege grant issues across your account. These queries find direct user-level grants (which should be on roles instead), PUBLIC role exposure, users with ACCOUNTADMIN, and roles with powerful account-level privileges.

**Account Context:** Execute these queries from the target account with ACCOUNTADMIN role.

## Why is this important?

Privilege grants are the mechanism by which roles gain access to objects. Common misconfigurations include:
- **Direct user grants** — Privileges granted directly to users bypass the role hierarchy and are invisible to role-based audits
- **PUBLIC role exposure** — Grants to PUBLIC give every user access, often unintentionally
- **ACCOUNTADMIN overuse** — Too many users with ACCOUNTADMIN defeats separation of duties
- **Excessive privileges** — Roles with MANAGE GRANTS or OWNERSHIP on account-level objects can escalate their own access

## Prerequisites

- Account accessible with ACCOUNTADMIN role
- SNOWFLAKE database IMPORTED PRIVILEGES granted

## Key Concepts

**Direct User Grants**
In Snowflake, privileges can be granted to either roles or users. Granting directly to users is an anti-pattern because it bypasses the role hierarchy. Access should always flow through roles.

**PUBLIC Role Grants**
Every user in the account automatically inherits all privileges granted to PUBLIC. Common issues include warehouse USAGE (compute costs), database/schema USAGE (metadata visibility), and table SELECT (data exposure).

**Account-Level Privileges**
High-risk privileges include:
| Privilege | Risk |
|-----------|------|
| MANAGE GRANTS | Can grant any privilege to any role |
| CREATE ROLE | Can create new roles |
| CREATE USER | Can create new users |
| EXECUTE TASK | Can run scheduled operations |
| MONITOR USAGE | Can view all account activity |

## Best Practices

- All access should flow through roles, never directly to users
- PUBLIC role should have minimal or no grants
- ACCOUNTADMIN should be limited to 2-3 break-glass users
- MANAGE GRANTS should only be on SECURITYADMIN
- Audit privilege grants quarterly at minimum

## How to Test

1. Run the direct user grants query — any results need immediate remediation
2. Review PUBLIC grants — each should have a documented business justification
3. Verify ACCOUNTADMIN count matches your break-glass policy

## More Information

* [Privilege Overview](https://docs.snowflake.com/en/user-guide/security-access-control-privileges) — Available privileges
* [GRANTS_TO_USERS](https://docs.snowflake.com/en/sql-reference/account-usage/grants_to_users) — User grant history
* [GRANTS_TO_ROLES](https://docs.snowflake.com/en/sql-reference/account-usage/grants_to_roles) — Role grant history
