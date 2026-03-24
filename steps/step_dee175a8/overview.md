<!-- Author: Richie Bachala (richie.bachala@snowflake.com) -->
In this step, you'll generate queries to discover and remediate privileges granted directly to users instead of to roles. Direct user grants bypass the RBAC hierarchy and create shadow access that is invisible to role-based audits.

**Account Context:** Execute from the target account with SECURITYADMIN role.

## Why is this important?

Snowflake allows privileges to be granted directly to users (e.g., `GRANT SELECT ON TABLE X TO USER jsmith`). This is an anti-pattern because:
- **Invisible to role audits** — Role hierarchy reviews miss user-level grants
- **Unmanageable at scale** — Each user must be individually managed
- **SCIM incompatible** — Identity providers manage role membership, not user-level privileges
- **Hard to revoke** — When a user changes teams, each direct grant must be individually revoked

## Prerequisites

- Task 1 (RBAC Assessment) completed — Step 1.2 identifies direct user grants
- Data Product functional roles exist (from Data Product Setup)
- Understanding of which roles each user's direct privileges should migrate to

## Key Concepts

**Role-Based vs. User-Based Access**
| Approach | Manageable | Auditable | SCIM Compatible |
|----------|-----------|-----------|-----------------|
| Grant to Role | Yes | Yes | Yes |
| Grant to User | No | Partially | No |

**Migration Pattern**
For each direct user grant:
1. Identify the correct role (READ, WRITE, CREATE, or ADMIN)
2. Verify the role already has the equivalent privilege
3. Revoke the direct user grant
4. Grant the role to the user (if not already granted)

## Best Practices

- Never grant privileges directly to users in production
- Use functional roles (READ/WRITE/CREATE/ADMIN) for all data access
- Use database roles (SC_R/SC_W/SC_C) for schema-level access when functional roles are too broad
- Audit direct user grants quarterly

## How to Test

1. Run the discovery query to find all direct user grants
2. For each grant, verify the target role has equivalent access
3. Revoke the direct grant and confirm the user can still access via their role

## More Information

* [Access Control Overview](https://docs.snowflake.com/en/user-guide/security-access-control-overview) — RBAC fundamentals
* [GRANT](https://docs.snowflake.com/en/sql-reference/sql/grant-privilege) — Granting privileges
* [REVOKE](https://docs.snowflake.com/en/sql-reference/sql/revoke-privilege) — Revoking privileges
