---
name: send-email
description: Use when drafting or sending ThaiBulkSMS email, choosing campaign versus transactional mail, or checking available mail capability.
---

Transactional email uses the mail channel, prepared at OAuth consent or provided
by an AI key pair. If `send_email` reports the mail channel is missing, follow
"If a channel is not ready" in get-started; do not ask for keys. For bulk email
to a list, use the campaigns skill.

Before `send_email`, read `get_mail_options` and `get_mail_credit`. Use the
reported default sender unless the user asked for another. If there is no single
default, show the returned senders and let the user pick, then pass its
`sender_id`. Do not ask the user to type a From address before reading options;
never invent an address or create a sender.

| Capability | Workflow |
| --- | --- |
| Mail channel (OAuth or AI key pair) | `get_mail_options`, `get_mail_credit` → `send_email` for one transactional email |
| Account OAuth | Console email campaign: owned sender/template, separate draft/update/submit (see campaigns) |

For draft-only intent, compose without sending. An explicitly requested persisted
Console draft uses `create_email_campaign`; it does not submit. Save reviewed
recipients with `update_email_campaign`; only explicit send intent permits
`submit_email_campaign` with the reviewed `expected_revision`. Transactional mail always
needs the mail channel; account login alone does not authorize it. Discover the campaign tool descriptions for their state details.

For transactional send, establish one approved `mail_from` contact and one `mail_to` contact
(each is an object with `email` and optional `name`), `subject` up to256 characters, and inline `html`
up to256KiB. `send_email` is email-only: no attachments, URL fetching, template
selection, SMTP mode or internal provider credentials. If attachments are required,
explain the unsupported requirement before sending anything.

Preserve Thai content. Assign one UUID `operation_id` per intended write and
retain identical arguments through reconnects. Drafting alone never authorizes
sending. Report accepted IDs/credit without claiming delivery; to confirm it, call
`get_delivery_status` with `channel: email` for status, opens and clicks. For uncertain
results, call `get_operation_status` with the original `tool_name` and
`operation_id`; reconcile without a fresh ID or automatic resend. Enter secrets
only through the client's secure setup or OAuth browser, never chat.
