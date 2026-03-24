<!-- Author: Richie Bachala (richie.bachala@snowflake.com) -->
In this step, you'll configure Snowflake Alerts that automatically detect and notify your security team when RBAC-relevant changes occur. This provides real-time visibility into privilege modifications that could weaken your security posture.

**Account Context:** Execute from the target account with SYSADMIN role.

## Why is this important?

Even after hardening your RBAC configuration, changes can reintroduce vulnerabilities:
- A new user gets ACCOUNTADMIN during an emergency and it's never revoked
- A developer grants SELECT directly to a user for quick debugging
- A new warehouse gets USAGE granted to PUBLIC by mistake
- Dormant users retain active roles for months after leaving the team

Alerts provide automated detection so your security team can respond before these issues compound.

## Prerequisites

- Step 4.1 (RBAC Monitoring Views) completed — alerts reference these views
- Notification integration configured (e.g., email, Slack, PagerDuty)
- Monitoring warehouse provisioned
- SYSADMIN role access

## Key Concepts

**Snowflake Alerts**
| Property | Description |
|----------|-------------|
| WAREHOUSE | Compute used to run the alert condition check |
| SCHEDULE | CRON expression defining check frequency |
| IF (EXISTS (...)) | Condition that must be true to fire the alert |
| THEN | Action to take (send notification) |

**Alert Configuration**
| Alert | Frequency | Severity |
|-------|-----------|----------|
| New ACCOUNTADMIN Grant | Hourly | Critical |
| Direct User Grant | Every 6 hours | High |
| PUBLIC Role Grant | Every 6 hours | High |
| Dormant Users | Weekly | Medium |

**Notification Integration**
Alerts send notifications via `SYSTEM$SEND_NOTIFICATION()`. You must have a notification integration configured:
```sql
CREATE NOTIFICATION INTEGRATION my_notification
  TYPE = QUEUE
  NOTIFICATION_PROVIDER = <provider>
  ...;
```

## Best Practices

- Start with critical alerts (ACCOUNTADMIN) and expand gradually
- Tune alert frequency based on your team's response capacity
- Use separate notification channels for different severity levels
- Review alert history monthly to ensure alerts are firing correctly
- Suppress known-good patterns to reduce alert fatigue

## How to Test

1. After creating alerts, check `SHOW ALERTS` to confirm they're active
2. Trigger a test: grant ACCOUNTADMIN to a test user
3. Wait for the alert schedule to fire
4. Verify the notification was received
5. Revoke the test grant

## More Information

* [Snowflake Alerts](https://docs.snowflake.com/en/user-guide/alerts) — Alert documentation
* [SYSTEM$SEND_NOTIFICATION](https://docs.snowflake.com/en/sql-reference/stored-procedures/system_send_notification) — Notification function
* [Notification Integrations](https://docs.snowflake.com/en/sql-reference/sql/create-notification-integration) — Setup guide
