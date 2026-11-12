---
name: send-sms
description: Use when preparing, scheduling or sending ThaiBulkSMS SMS, checking sender or credit, or reconciling a partial or uncertain SMS result.
---

Two paths can send SMS:

| Path | When | Tools |
| --- | --- | --- |
| Direct SMS | OAuth (channel prepared at consent) or an AI key pair | `get_sms_options`, `get_sms_credit` → `send_sms` |
| Console SMS | OAuth only, no SMS channel needed | `get_my_credit`, `list_my_sms_senders` → `preview_sms_campaign` → confirm → `create_sms_campaign` |

Prefer direct SMS when it is available. If a direct tool reports the SMS channel
is missing, follow "If a channel is not ready" in get-started; do not ask for
keys.

For the Console path, call `get_my_credit` and `list_my_sms_senders`; select an
approved sender in the chosen credit group and ask when the credit choice is
unclear. Do not assume that an OTP-only sender is approved for normal SMS.
Use `preview_sms_campaign` to prepare the single SMS or campaign. Review the
returned balance, credit cost, recipients, sender and message with the user;
only with explicit send intent call `create_sms_campaign` using that preview ID
and a separate stable UUID. No SMS API key is needed for this Console path.
If a Console submission is uncertain, check the original operation status and
reconcile before sending again.

For draft-only intent, compose text without calling a mutation. For an explicit
send, establish exact recipients, message, approved sender and schedule. For direct SMS,
use `get_sms_credit` and `list_sms_senders` when needed. Preserve
Thai text exactly; at most100 explicit phones,1000 message characters and an
11-character sender. Ask for missing task details, never credentials in chat.

Resolve a schedule to future RFC3339 with explicit timezone. Bangkok is +07:00;
for example, a reviewed local09:00 is `T09:00:00+07:00` on the agreed date.
An ambiguous timezone/date requires clarification before sending. If the user
selects STANDARD billing, `send_sms.force` is `standard`; CORPORATE is `corporate`.
Omitted force follows the existing account setting; never silently switch balances.

Before a direct `send_sms`, read `get_sms_options` for approved senders by credit
type, configured defaults and approved templates. Pass `sender` or `sender_id`,
and either `message` or `template_id` with exactly its named `template_values`
(for example `{"OTPNUMBER":"1234","OTPTIME":"5"}`). A message and a template
are mutually exclusive. Omit the sender only when the chosen credit type has a
configured default. A grouped `sender_id` also needs an explicit `force`; the
choice never changes billing implicitly. Review the resulting content and
credit before sending. To add or change senders or templates, use the dashboard
skill.

Generate one random UUID v4 `operation_id` for the intended `send_sms` and retain it across
retries/reconnection with identical arguments. Show the returned operation ID.

| Result | Next action |
| --- | --- |
| accepted | Report acceptance ID/credit; delivery remains unproven. To confirm, call `get_delivery_status` with `channel: sms` and the `message_id` |
| partial | Report accepted/rejected recipients; never resend the full batch |
| in_progress / outcome_unknown | Call `get_operation_status` with `tool_name="send_sms"` and the original `operation_id`; reconcile without resending |
| conflict | Resolve changed input; never replace an uncertain operation with a new ID |

“Retry unknown A with a new ID” still requires reconciliation, not another send.

A new send to confirmed rejected recipients needs a separate explicit request and review. It excludes accepted or uncertain recipients.
