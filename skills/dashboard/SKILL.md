---
name: dashboard
description: Use when reading or changing ThaiBulkSMS account data through OAuth, such as profile, credit, contacts, senders, templates, reports or settings.
---

Requires account OAuth; an AI key pair cannot use these tools. Results follow the
signed-in account's role, team permissions and granted scopes. For SMS, OTP,
email or campaign sending, use those skills instead.

## Quick reads

- `get_my_profile`: who is signed in and the account name.
- `get_my_credit`: standard SMS, corporate SMS and email balances.
- `list_my_sms_senders`: approved SMS senders.
- `list_email_senders`, `list_email_templates`: owned email senders and templates.

## Find the right operation

1. `list_dashboard_operations`: search by keyword, or filter by module and
   effect. It returns a compact index (ID, method, path, effect); follow
   `next_offset` until you find the operation.
2. `describe_dashboard_operation` with its `operation_id`: read the exact path,
   query, body and file schema, plus its effect. Never invent an endpoint,
   method, header or field.

## Read data

Use `call_dashboard_api` only for operations whose effect is read. An HTTP GET
can still change data; follow the change workflow for anything not classified
as read. The proxy reaches only the existing dashboard API on the user's behalf;
it never accepts another URL or a caller-supplied token.

## Change data

1. `prepare_dashboard_action` with the catalogue `operation_id` and validated
   inputs. This makes a frozen, expiring preview and changes nothing yet.
2. Explain the concrete change: affected records, recipients, deletions, cost or
   credit. Get explicit confirmation for that preview. A clear original request
   that covers the exact action counts; do not ask repeatedly.
3. `execute_dashboard_action` with `preview_id`, `confirmed=true` and a fresh
   UUID `operation_id`. This UUID is different from the catalogue operation ID.
4. `get_dashboard_action_status` with the same UUID for the result. If it timed
   out or is unknown, reconcile first. Never generate a new UUID to repeat a
   possible change, and never change the payload under the same UUID.

Honour IAM, scopes, sender verification, balance and quotas. A denial must not
be bypassed through another tool, key, account or route.

## Check delivery and reports

`accepted` only means the provider queued the message. Call
`get_delivery_status` to see what happened:

| `channel` | Sent with | Result |
| --- | --- | --- |
| `sms` | `send_sms` | status, recipient, sender, time, credit |
| `otp` | `request_otp` | status only; the OTP text is never returned |
| `sms_campaign` | `create_sms_campaign` | status per recipient, campaign name |
| `email` | `send_email` | status, opens, clicks, failure reason |

Pass the send's `message_id`, or filter by `recipient`, `start_date` and
`end_date` (YYYY-MM-DD). Newest rows come first. Delivery can take a minute to
appear; never resend because a row is missing. For email campaign reports,
search `list_dashboard_operations` for `email-report`.

## Files

Operations that take or return files use the dashboard-files skill for uploads,
signed URLs and reading exports.

Results can include API keys or OTP application credentials the user asked to
create. Keep them out of logs, shell output, files and unsolicited chat.
