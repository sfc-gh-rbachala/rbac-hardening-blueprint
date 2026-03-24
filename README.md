# RBAC Hardening Blueprint

A [Blueprint Manager](https://github.com/Snowflake-Labs/blueprint-manager) blueprint that audits, hardens, and monitors role-based access controls (RBAC) across a Snowflake account.

## What It Does

This blueprint walks you through securing access controls in four phases:

1. **RBAC Assessment** -- Run read-only audit queries to map your role hierarchy, discover risky privilege grants (PUBLIC exposure, excessive ACCOUNTADMIN holders), and detect stale access (dormant users, unused roles).

2. **Privilege Hardening** -- Tighten access based on assessment findings: lock down the PUBLIC role, restrict ACCOUNTADMIN to break-glass users, migrate direct user-level grants into roles, and convert schemas to managed access.

3. **User & Service Account Provisioning** -- Assign end users to functional roles (READ/WRITE/CREATE/ADMIN) and create dedicated service accounts with key-pair authentication and least-privilege access.

4. **Monitoring & Compliance** -- Establish ongoing governance with monitoring views, privilege change alerts, and a compliance review report on a weekly/monthly/quarterly cadence.

## Blueprint Structure

This repo mirrors the [blueprint-manager-core](https://github.com/Snowflake-Labs/blueprint-manager-core) format:

```
blueprints/
  blueprint_e2a16d54.yaml        # Blueprint definition (4 tasks)
tasks/
  task_5e1d42ce.yaml             # Task 1: RBAC Assessment
  task_5fa4ca80.yaml             # Task 2: Privilege Hardening
  task_de441223.yaml             # Task 3: User & Service Account Provisioning
  task_852e9e87.yaml             # Task 4: Monitoring & Compliance
steps/
  step_4cbf959a/                 # 1.1 Audit Role Hierarchy
  step_e9625d2d/                 # 1.2 Audit Privilege Grants
  step_aa89bd0a/                 # 1.3 Audit Stale Access
  step_7c594236/                 # 2.1 Restrict PUBLIC Role
  step_20b9f1a5/                 # 2.2 Enforce Admin Separation
  step_dee175a8/                 # 2.3 Revoke Direct User Privileges
  step_42c202cb/                 # 2.4 Enforce Managed Access Schemas
  step_a9ede2db/                 # 3.1 Assign Functional Roles
  step_fe647358/                 # 3.2 Provision Service Accounts
  step_de1f6f9d/                 # 4.1 Create RBAC Monitoring Views
  step_d5da1676/                 # 4.2 Configure Privilege Change Alerts
  step_afdc2cf4/                 # 4.3 Generate Access Review Report
definitions/
  questions.yaml                 # 12 questions that drive template rendering
samples/
  answers_rbac_hardening.yaml    # Example answers for testing
```

Each step directory contains four files:
- `metadata.yaml` -- Step ID, slug, title, visibility conditions
- `overview.md` -- Static documentation (no Jinja)
- `dynamic.md.jinja` -- Jinja-templated markdown based on user answers
- `code.sql.jinja` -- Jinja-templated SQL that generates the actual Snowflake commands

## Key Design Decisions

- **Review-before-execute**: All hardening steps generate SQL for review. Nothing destructive runs automatically.
- **Scope control**: Audit the full account or target specific data product role hierarchies.
- **Conditional steps**: PUBLIC role restriction, managed access enforcement, and alert configuration are gated by user answers.
- **Complements Data Product Setup**: Designed to run after the Data Product Setup blueprint creates the role hierarchy.
- **Repeatable**: Run periodically to maintain RBAC hygiene as the account evolves.

## Contributing

This blueprint is submitted as a PR to [blueprint-manager-core](https://github.com/Snowflake-Labs/blueprint-manager-core/pull/8). This standalone repo is provided for reference and review.

## Author

Richie Bachala (richie.bachala@snowflake.com)
