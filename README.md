# ThaiBulkSMS

Send SMS, one-time passwords and email from your assistant with your existing
[ThaiBulkSMS](https://www.thaibulksms.com) and ThaiBulkMail account. The plugin
adds seven skills and connects to the hosted ThaiBulkSMS server; there is
nothing to run on your machine.

With it, your assistant can:

- check SMS and email credit, and list approved senders and templates
- send an SMS, or request and verify an OTP
- send a transactional email
- prepare, schedule and follow up SMS and email campaigns
- read reports and change account settings your sign-in is allowed to use
- tell you whether a message was delivered

Every send shows the recipients, content, sender and cost first and waits for
your approval. Messages use your own credit, and your account permissions
decide what the assistant can see and do.

## Install

**Claude (claude.ai, Cowork, Claude Code):** add ThaiBulkSMS from the
[directory](https://claude.ai/directory). In Claude Code you can also install
straight from this repository:

```
/plugin install thaibulksms --marketplace thaibulksms/tbs-ai-mcp
```

**ChatGPT and Codex:** add ThaiBulkSMS from the
[plugin directory](https://chatgpt.com/plugins/).

**Any other MCP client:** add `https://ai.thaibulksms.com/mcp` as a remote (Streamable HTTP)
server. Setup for each client is at [https://ai.thaibulksms.com/agents](https://ai.thaibulksms.com/agents).

## Sign in

The first time a tool is used, your browser opens the ThaiBulkSMS sign-in page.
Sign in with your ThaiBulkSMS account and approve the permissions listed. The
plugin holds no password or API key, and you should never paste one into a
chat. The connection stays signed in while you use it and ends after seven days
without use. You can disconnect at any time from your assistant's connector
settings.

## Skills

| Skill | Use it to |
| --- | --- |
| `get-started` | Connect an account and choose the SMS, OTP and email channels to use |
| `send-sms` | Send an SMS and check its delivery |
| `verify-otp` | Request a one-time password and verify the code |
| `send-email` | Send a transactional email |
| `campaigns` | Prepare, review and schedule SMS and email campaigns |
| `dashboard` | Read reports and change account settings |
| `dashboard-files` | Upload contact files and download exports |

## What the plugin connects to

The plugin declares one remote MCP server, `https://ai.thaibulksms.com/mcp`, operated by
ThaiBulkSMS. Requests go only to that server, over HTTPS, signed in with OAuth.
The plugin runs no code on your machine: it has no hooks, scripts or local
servers, and it reads no files or environment variables.

What you ask the assistant to send (recipients, message text, email content,
uploaded contact files) goes to ThaiBulkSMS to carry out the request, as it
would from the ThaiBulkSMS website. See the [privacy notice](https://ai.thaibulksms.com/privacy).

## Security

The plugin stores and asks for no credentials. If you find a security problem
in the plugin or the service, please report it privately through
[ThaiBulkSMS support](https://www.thaibulksms.com/contact-us/) rather than a public issue.

## Links

- Website and setup guides: https://ai.thaibulksms.com
- Privacy: https://ai.thaibulksms.com/privacy
- Terms: https://ai.thaibulksms.com/terms
- Support: https://www.thaibulksms.com/contact-us/

## License

The files in this repository are released under the [MIT License](LICENSE).
The ThaiBulkSMS service they connect to is a separate commercial service,
covered by its own [terms](https://ai.thaibulksms.com/terms); its source code is not part of this
repository.
