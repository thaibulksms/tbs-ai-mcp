---
name: verify-otp
description: Use when a user explicitly requests a ThaiBulkSMS business OTP or provides a business OTP PIN for verification.
---

Use `request_otp`, `verify_otp`, `get_otp_options` and `get_operation_status`.
They need the OTP channel, prepared at OAuth consent or provided by an AI key
pair. If the OTP channel is missing, follow "If a channel is not ready" in
get-started; do not ask for keys. An SMS channel cannot replace it. Never send
an OTP merely to test a connection.

Read `get_otp_options` first: the application fixes its sender, template, brand,
PIN length and expiry, and `request_otp` cannot override them. To use a different
sender or template, change the OTP application with the dashboard skill, then
read the options again. Never generate a PIN locally or switch to SMS instead.
The end user reads the PIN on their phone; pass it with the returned token to
`verify_otp`. Never ask the account owner for the PIN unless they are that user.

For explicit request intent, establish the exact phone and call `request_otp`
with one UUID `operation_id`. Return the token/reference, never a generated PIN.
Acceptance does not prove delivery; if the user reports no SMS, call
`get_delivery_status` with `channel: otp` and the phone as `recipient` before
requesting again.

Verify only tokens returned by this AI service's request operation, with the
same actor, client and full OTP application pair. External tokens are unsupported
even with a valid pair. Bindings last at most one hour and no longer than login.
Preserve a user-entered PIN such as `"001234"` as a string; pass it only to
`verify_otp` with the returned token and its own stable UUID operation ID.
Do not echo or persist the PIN. This workflow never handles Account login MFA.

Request and verification can consume credit or attempt state. Never retry
transparently. For an uncertain result, use `get_operation_status` with the
original `tool_name` and `operation_id`; do not generate a replacement operation.
Report generic rejection accurately rather than guessing wrong-PIN versus expiry.
On429, wait; no automatic verification or request retry is authorized.

Tool inputs: `request_otp` takes `phone` and `operation_id`; `verify_otp` takes `token`, `pin` and `operation_id`.
