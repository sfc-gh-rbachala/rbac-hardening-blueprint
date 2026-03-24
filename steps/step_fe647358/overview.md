<!-- Author: Richie Bachala (richie.bachala@snowflake.com) -->
In this step, you'll create and configure service accounts for automated workloads (ETL pipelines, BI tools, applications). Service accounts use key-pair authentication instead of passwords and are assigned dedicated functional roles and warehouses.

**Account Context:** Execute from the target account with USERADMIN (user creation) and SECURITYADMIN (role grants).

## Why is this important?

Service accounts differ from human user accounts in important ways:
- **No interactive login** — They authenticate via key-pair (RSA), not passwords
- **Dedicated warehouses** — Their compute consumption is tracked and budgeted separately
- **Least-privilege roles** — They get exactly the access needed for their workload
- **No MFA** — Key-pair authentication provides equivalent security without MFA

Without dedicated service accounts:
- Shared credentials create accountability gaps
- Human users' credentials are used in automation (revocation breaks pipelines)
- Compute costs are impossible to attribute to specific workloads

## Prerequisites

- Data Product roles exist (from Data Product Setup)
- Designated warehouses exist for each service account
- RSA key pairs generated for each service account (2048-bit minimum)
- Service account specifications approved (name, purpose, role, warehouse)

## Key Concepts

**Service Account Properties**
| Property | Value | Reason |
|----------|-------|--------|
| TYPE | SERVICE | Distinguishes from human users |
| DEFAULT_ROLE | Functional role | Auto-activates on connection |
| DEFAULT_WAREHOUSE | Dedicated WH | Isolates compute costs |
| RSA_PUBLIC_KEY | Base64-encoded | Key-pair authentication |
| MUST_CHANGE_PASSWORD | FALSE | Not using password auth |

**Key-Pair Authentication**
```bash
# Generate key pair (run locally, NOT in Snowflake)
openssl genrsa 2048 | openssl pkcs8 -topk8 -inform PEM -out rsa_key.p8 -nocrypt
openssl rsa -in rsa_key.p8 -pubout -out rsa_key.pub
```

The public key (contents of `rsa_key.pub`, without headers) is stored in Snowflake. The private key is stored securely in your secrets manager.

## Best Practices

- Name service accounts with a consistent prefix (e.g., `SVC_`, `SA_`)
- One service account per workload/pipeline
- Rotate RSA keys annually
- Monitor service account activity with the views in Task 4
- Never assign ACCOUNTADMIN or SECURITYADMIN to a service account
- Set `DAYS_TO_EXPIRY` for temporary service accounts

## How to Test

1. Connect using the service account's private key
2. Verify the default role is automatically activated
3. Verify the default warehouse is used
4. Confirm the service account can only access objects within its role's scope

## More Information

* [Key-Pair Authentication](https://docs.snowflake.com/en/user-guide/key-pair-auth) — Setup guide
* [CREATE USER](https://docs.snowflake.com/en/sql-reference/sql/create-user) — User properties
* [Service Users](https://docs.snowflake.com/en/user-guide/admin-user-management#service-users) — Service account best practices
