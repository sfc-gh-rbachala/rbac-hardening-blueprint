<!-- Author: Richie Bachala (richie.bachala@snowflake.com) -->
In this step, you'll generate SQL to revoke unnecessary grants from the PUBLIC role. Since PUBLIC is automatically granted to every user, any privilege on PUBLIC is effectively given to all users in the account.

**Account Context:** Execute from the target account with SECURITYADMIN role.

## Why is this important?

The PUBLIC role is one of the most common sources of excessive access:
- **Warehouse USAGE** — Any user can consume compute credits
- **Database/Schema USAGE** — Any user can see object metadata and structure
- **SELECT on tables/views** — Data exposed to everyone in the account
- **USAGE on functions/procedures** — Any user can execute shared code

These grants often accumulate from quick demos, testing, or legacy configurations and are rarely reviewed.

## Prerequisites

- Task 1 (RBAC Assessment) completed — specifically Step 1.2 (Audit Privilege Grants)
- PUBLIC grant exceptions documented
- Agreement from stakeholders on which grants to revoke

## Key Concepts

**PUBLIC Role Behavior**
- Every user automatically holds the PUBLIC role — it cannot be revoked
- Privileges granted to PUBLIC are inherited by all users
- PUBLIC role grants bypass the RBAC hierarchy entirely

**Exception Handling**
Some PUBLIC grants are intentional:
- Sample data databases (SNOWFLAKE_SAMPLE_DATA)
- Shared reference databases
- Utility functions used by all users

Configure exceptions via `rbac_public_exceptions` to exclude these from revocation.

## Best Practices

- Default to revoking all PUBLIC grants, then add back only what's needed
- Document the business justification for every exception
- Re-audit PUBLIC grants after any new database or schema creation
- Consider network policies as an additional layer on top of PUBLIC restrictions

## How to Test

1. Before revoking: run `SHOW GRANTS TO ROLE PUBLIC;` and save the output
2. After revoking: run the same command and confirm only exceptions remain
3. Test with a user who only has PUBLIC role to verify they cannot access restricted objects

## More Information

* [PUBLIC Role](https://docs.snowflake.com/en/user-guide/security-access-control-overview#public-role) — Role behavior
* [REVOKE](https://docs.snowflake.com/en/sql-reference/sql/revoke-privilege) — Revoking privileges
