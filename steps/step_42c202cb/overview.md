<!-- Author: Richie Bachala (richie.bachala@snowflake.com) -->
In this step, you'll convert existing schemas to use Snowflake's Managed Access feature, which centralizes privilege management by restricting GRANT authority to the schema owner (or SECURITYADMIN), instead of allowing any role with ownership to manage grants.

**Account Context:** Execute from the target account with SECURITYADMIN role.

## Why is this important?

Without Managed Access, any role that owns an object within a schema can grant access to that object. This leads to:
- **Shadow access patterns** — Object owners can bypass RBAC by granting directly
- **Privilege sprawl** — Grants proliferate without centralized control
- **Audit gaps** — Security teams cannot guarantee that only approved roles have access

With `MANAGED ACCESS`, only the schema owner and SECURITYADMIN (or higher) can manage grants on objects within the schema, regardless of who owns those objects.

## Prerequisites

- Task 1 (RBAC Assessment) completed
- Steps 2.1–2.3 completed (PUBLIC restricted, admin separated, direct grants revoked)
- Understanding of which schemas should be converted (all data-product schemas recommended)

## Key Concepts

**Managed Access vs. Standard Schemas**
| Feature | Standard Schema | Managed Access Schema |
|---------|----------------|----------------------|
| Object owner can GRANT | Yes | **No** |
| Schema owner can GRANT | Yes | Yes |
| SECURITYADMIN can GRANT | Yes | Yes |
| Privilege sprawl risk | High | **Low** |
| Central control | No | **Yes** |

**Applying Managed Access**
- New schemas: `CREATE SCHEMA <name> WITH MANAGED ACCESS;`
- Existing schemas: `ALTER SCHEMA <name> ENABLE MANAGED ACCESS;`
- Reverting (not recommended): `ALTER SCHEMA <name> DISABLE MANAGED ACCESS;`

## Best Practices

- Enable Managed Access on **all production schemas**
- Apply at schema creation time via Data Product Setup templates
- Do not disable Managed Access once enabled unless absolutely necessary
- Monitor for schemas created without Managed Access using the monitoring views in Task 4

## How to Test

1. In a Managed Access schema, attempt to GRANT from an object-owner role (should fail)
2. Verify SECURITYADMIN can still GRANT on objects in the schema
3. Verify schema owner can still GRANT on objects in the schema

## More Information

* [Managed Access Schemas](https://docs.snowflake.com/en/user-guide/security-access-control-overview#managed-access-schemas) — Feature documentation
* [ALTER SCHEMA](https://docs.snowflake.com/en/sql-reference/sql/alter-schema) — Enabling managed access
