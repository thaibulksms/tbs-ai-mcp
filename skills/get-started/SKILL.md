---
name: get-started
description: Use when connecting ThaiBulkSMS to an assistant, fixing a missing SMS, OTP or email channel, or resolving login, scope or tool-discovery problems.
---

# ThaiBulkSMS

Public bilingual guide: https://ai.thaibulksms.com/agents
MCP endpoint: https://ai.thaibulksms.com/mcp (Streamable HTTP)

ตอบเป็นภาษาไทยหรือภาษาอังกฤษตามภาษาที่ผู้ใช้เลือก
ใช้บัญชี ThaiBulkSMS เดิมเข้าสู่ระบบด้วย OAuth ผ่านเบราว์เซอร์ ระบบเตรียม SMS, OTP และอีเมลให้อัตโนมัติ
ห้ามขอรหัสผ่าน JWT, CAPTCHA, MFA, PIN หรือ API secret ในแชต
ก่อนเปลี่ยนข้อมูลหรือส่งข้อความ ต้องแสดงรายละเอียดและขอให้ผู้ใช้ยืนยัน
หากไม่ทราบผล ให้ตรวจสอบ operation ID เดิม ห้ามสร้างคำขอใหม่เพื่อทำงานซ้ำ

## What to use next

| The user wants to | Skill |
| --- | --- |
| Send one or more SMS, or check SMS sender/credit | send-sms |
| Request or verify a business OTP | verify-otp |
| Send a transactional email | send-email |
| Prepare or submit an SMS or email campaign | campaigns |
| Read or change account data: profile, credit, contacts, senders, templates, reports, settings | dashboard |
| Check whether a message was delivered, opened or clicked | dashboard |
| Upload, read or export files for an account operation | dashboard-files |

Never send SMS, request an OTP or send email merely to test a connection.
Verify with read-only status, profile, option or credit calls.

Every write uses one fresh, randomly generated UUID v4 `operation_id`; never a
patterned, sequential or example ID. Repeating it within 24 hours
returns the original receipt without sending again. An uncertain result blocks
its own repeat for 4 hours; check it with `get_operation_status` and
`get_delivery_status`, never with a new ID. An account can hold 10 uncertain
operations at once; further sends wait until one is resolved or released.
After a receipt expires, check delivery and get new explicit intent before
sending again.

## Choose how to connect

Recommend account OAuth. It gives every tool (dashboard, campaigns, SMS, OTP and
email) with one sign-in and no keys to handle. First call `get_connection_status`
and reuse an active connection.

## Connect with OAuth

1. Preserve the client's existing configuration and unrelated MCP servers. Add
   https://ai.thaibulksms.com/mcp as a remote Streamable HTTP server. Clients
   register themselves; no client ID is needed. For Claude Code:
   `claude mcp add --transport http thaibulksms https://ai.thaibulksms.com/mcp`,
   then open `/mcp`, select `thaibulksms` and choose Authenticate. Other clients
   need Streamable HTTP with OAuth and PKCE; examples are in the public guide.
   On a headless machine, the user pastes the final browser URL into the
   client's own prompt, not the chat.
2. The user signs in with their usual ThaiBulkSMS account in the browser,
   including CAPTCHA and MFA, and approves the requested permissions.
3. Approval prepares the granted SMS, OTP and email channels automatically. The
   server reuses or creates one managed key per service and keeps it encrypted.
   The client keeps using OAuth only: no keys, headers or reveals.
4. Check with `get_connection_status` and `get_connection_profile`. The client
   refreshes tokens itself; reauthorize only when the server asks for login again.

The account JWT stays server-side. Never extract it, add static auth headers or
put tokens in URLs.

## If a channel is not ready

A channel tool can report a missing SMS, OTP or email channel when setup at
consent did not finish. Repair it without asking the user for keys or choices:

1. Call `get_ai_connection`.
2. No connection: call `create_ai_connection` and omit `connections`. Defaults
   are all six channel scopes and 100 per channel per day.
3. Existing connection: call `update_ai_connection` with its current `version`
   and its current scopes, limits, expiry and CIDRs unchanged; omit `connections`.
   This repairs missing channels and keeps the key pair and today's usage.
4. Use one fresh UUID `operation_id`; if the result is uncertain, read its status
   before retrying. Setup creates credentials only and sends nothing.
5. OTP uses the account's registered contact email and company name. Ask for
   `contact_email` or `brand_name` only when the result says they are missing.

A missing OAuth scope or team permission is not a channel problem: reauthorize
through the client and ask the owner to grant it. Never switch account,
authentication mode or key to bypass a denial.

## Key-only clients

This plugin signs in with OAuth and needs no key. Access with an AI key pair,
for clients that cannot use OAuth, is set up outside the plugin:
https://ai.thaibulksms.com/agents?channel=ai

## Tools missing in the client

If advertised tools are missing or return `Unknown tool`, the host's tool list
is stale. Refresh the ThaiBulkSMS connection in the client, then start a new
conversation. Do not log out, rotate keys or retry a send to fix discovery. In
ChatGPT: https://developers.openai.com/plugins/deploy/connect-chatgpt#refresh-metadata

## Client references

- Claude Code: https://code.claude.com/docs/en/mcp
- Codex: https://learn.chatgpt.com/docs/extend/mcp?surface=cli
- Cursor: https://cursor.com/docs/mcp

Privacy: https://ai.thaibulksms.com/privacy
Terms: https://ai.thaibulksms.com/terms
Support: https://www.thaibulksms.com/contact-us/
