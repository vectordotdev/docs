---
description: Granular access control to your Timber resources
---

# Role Based Access Control

Timber implements a [role based access control system](https://en.wikipedia.org/wiki/Role-based_access_control) \(RBAC\) providing for granular access control across your Timber account. Currently, Timber only offers two pre-built roles -- `admin` and `member`.

## Permissions

{% tabs %}
{% tab title="Account" %}
| Resource | Permission | Member | Admin |
| :--- | :--- | :---: | :---: |
| API Keys | `create_api_keys` | X | ✔ |
|  | `delete_api_keys` | X | ✔ |
|  | `read_api_keys` | X | ✔ |
|  | `update_api_keys` | X | ✔ |
| Memberships | `create_memberships` | X | ✔ |
|  | `delete_memberships` | X | ✔ |
|  | `read_memberships` | X | ✔ |
|  | `update_memberships` | X | ✔ |
{% endtab %}

{% tab title="Billing" %}


| Resource | Permission | Member | Admin |
| :--- | :--- | :---: | :---: |
| [Billing Accounts](billing.md) | `read_billing_accounts` | X | ✔ |
|  | `update_billing_accounts` | X | ✔ |
| [Billing Invoices](billing.md#invoices) | `read_billing_invoices` | X | ✔ |
| [Billing Payment Methods](billing.md#payments) | `create_billing_payment_methods` | X | ✔ |
|  | `delete_billing_payment_methods` | X | ✔ |
|  | `read_billing_payment_methods` | X | ✔ |
|  | `update_billing_payment_methods` | X | ✔ |
| Billing Subscriptions | `read_billing_subscriptions` | X | ✔ |
|  | `update_billing_subscriptions` | X | ✔ |
{% endtab %}

{% tab title="Collaboration" %}
| Resource | Permission | Member | Admin |
| :--- | :--- | :---: | :---: |
| [Alerts](../alerting.md) | `create_alerts` | ✔ | ✔ |
|  | `delete_alerts` | ✔ | ✔ |
|  | `read_alerts` | ✔ | ✔ |
|  | `update_alerts` | ✔ | ✔ |
| [Applications / Sources](../source-management.md) | `create_applications` | ✔ | ✔ |
|  | `delete_applications` | ✔ | ✔ |
|  | `read_applications` | ✔ | ✔ |
|  | `update_applications` | ✔ | ✔ |
| [Archives](../archiving.md) | `read_log_archives` | ✔ | ✔ |
| [Log Lines](../live-tailing.md) | `read_log_lines` | ✔ | ✔ |
| [Notification Destinations](../alerting.md#notification-destinations) | `create_notification_destinations` | ✔ | ✔ |
|  | `delete_notification_destinations` | ✔ | ✔ |
|  | `read_notification_destinations` | ✔ | ✔ |
|  | `update_notification_destinations` | ✔ | ✔ |
| [Saved Views](../saved-views.md) | `create_saved_views` | ✔ | ✔ |
|  | `delete_saved_views` | ✔ | ✔ |
|  | `read_saved_views` | ✔ | ✔ |
|  | `update_saved_views` | ✔ | ✔ |
| [SQL Queries](../sql-querying.md) | `create_sql_queries` | ✔ | ✔ |
|  | `delete_sql_queries` | ✔ | ✔ |
|  | `read_sql_queries` | ✔ | ✔ |
|  | `update_sql_queries` | ✔ | ✔ |
{% endtab %}
{% endtabs %}

## API Keys

[API keys](api-keys.md) are created on the organization level and have `admin` level access. This is why API keys can only be created by other `admin`. For more information on API keys please see the [API keys section](api-keys.md).

## Changing Roles

`admin` can change the role of other members within their organization. Head over to the [Team Management section](team-management.md) for more information on managing your team.

