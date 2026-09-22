# Privacy Policy: Brendly plugin for Claude

**Last updated:** 23 September 2026

This policy explains what the Brendly plugin and the Brendly MCP server (`https://mcp.brendly.rs`)
collect, why, how long it is kept, and what your rights are. It covers only the plugin and its
server. Your use of the Brendly platform itself is covered by the
[Brendly Privacy Policy](https://brendly.rs/pomoc/politika-privatnosti/).

## 1. Who we are

The data controller is:

**BRANDIT TECHNOLOGY DOO BEOGRAD-Vračar**
Vojvode Hrvoja 24, Vračar, Belgrade, Serbia
Company number (MB): 21495646
Tax number (PIB): 111523347

Contact for any privacy question or request: **podrska@brendly.rs**

## 2. What the plugin is

The plugin lets Claude act on your Brendly seller account on your behalf: reading and changing
products, designs, categories, prices, storefront themes, discounts, orders, payouts and settings.
It performs only actions you can already perform yourself in the Brendly web application.

The plugin has two parts:

- **Instructions (skills)** bundled with the plugin. These are static text files. They send
  nothing anywhere.
- **The Brendly MCP server**, which receives the tool calls Claude makes and forwards them to the
  Brendly platform API.

## 3. What we do not collect

We want to be explicit about this, because it is the question people ask first.

- **We do not receive or store your conversation with Claude.** Your prompts, Claude's replies,
  attachments and conversation history are not sent to our server and are not stored by us.
- **We do not log the arguments of tool calls.** What you asked Claude to write into a product or
  a theme is sent to the Brendly platform to be applied, but it is not written into our logs.
- **We never receive your password.** You sign in on the Brendly platform itself. Neither Claude
  nor this plugin ever sees your password.
- **We do not receive payment card data.** Payment setup happens in the Brendly platform.
- **We do not sell or rent personal data, and we do not use it for advertising or profiling.**
- **We do not read Claude's memory, chat history, conversation summaries or your uploaded files.**

## 4. What we store

| Data | Why | How long |
|---|---|---|
| **Session**: your Brendly user id, username (email address), account roles, your Brendly API tokens **encrypted at rest**, and the shop you selected | To act on your account without asking you to sign in again for every message | Deleted 14 days after your last use, or immediately when you revoke access |
| **OAuth records**: registered client applications, and hashed authorization codes, access tokens and refresh tokens | To run the sign-in flow securely | Authorization codes 5 minutes, access tokens 1 hour, refresh tokens rotated on every use; all deleted automatically once expired |
| **Connection record**: the name and version of the MCP client you connect with, the capabilities it declares, and the protocol version | To keep a long-running task working across restarts and to stay compatible with different clients | Deleted after a period of inactivity |
| **Activity log**: for each tool call, which tool and action ran, which market and shop it concerned, whether it succeeded, how long it took, and your user id and username | Security, abuse prevention, and diagnosing failures | See section 5 |
| **Error text**: when a call fails or is blocked, up to the first 300 characters of the resulting error message | To diagnose what went wrong | See section 5 |
| **Feedback**, only when you ask Claude to send it: your message in your own words, Claude's short description of the situation, and the metadata (not the content) of your last 15 tool calls | To act on problems and requests you report to us | See section 5 |
| **Upload records**: a short-lived token and file metadata when you upload an image through the browser | To complete the upload | Expires shortly after the upload |

Successful tool calls do not store any result text. Error text is stored only for calls that fail
or are blocked, and it is the error message produced by our own service.

## 5. Retention of activity logs and feedback

Activity logs and feedback are kept for as long as they are useful for security, auditing and
support. **We do not currently apply an automatic deletion schedule to these two records.** You
can ask us to delete them at any time by writing to podrska@brendly.rs, and we will do so unless
we are required by law to keep a specific record.

Everything else listed in section 4 is deleted automatically, on the schedule shown there.

## 6. Where your data is held

The server and its database run on Amazon Web Services in the European Union (Frankfurt,
`eu-central-1`). Data is not transferred outside the EU by us.

## 7. Other parties involved

- **Anthropic** operates Claude and processes your conversation under
  [Anthropic's own privacy policy](https://www.anthropic.com/legal/privacy). Your conversation
  reaches us only as the specific tool calls Claude makes.
- **Amazon Web Services** hosts the server and database as our infrastructure provider.
- **The Brendly platform**, operated by us, receives and applies the changes you ask for.

If you choose to use a separate image generation tool alongside the plugin, that tool is your own
arrangement with its provider. We are not a party to it and we receive nothing from it except any
image file you or Claude uploads to your Brendly account.

## 8. Legal basis

We process this data to perform the service you asked for (contract), to keep the service secure
and to prevent abuse (legitimate interest), and, for feedback, on the basis of your own request.
We process personal data in line with the EU General Data Protection Regulation and the Serbian
Personal Data Protection Act.

## 9. Your rights and how to exercise them

You may ask us for a copy of your data, for correction, for deletion, for restriction of
processing, or object to processing. Write to **podrska@brendly.rs**.

You can cut off the plugin's access yourself at any time:

- **Claude app:** Settings, then Connectors, then remove Brendly.
- **Claude Code:** `/mcp`, then disconnect Brendly, or uninstall the plugin.

Removing access stops all further processing. Your session and tokens are deleted. To have the
activity log and any feedback deleted as well, write to us.

You also have the right to complain to a supervisory authority: in Serbia, the Commissioner for
Information of Public Importance and Personal Data Protection; in the EU, your national data
protection authority.

## 10. Children

The plugin is for Brendly sellers and is not intended for anyone under 18.

## 11. Security

Your Brendly API tokens are encrypted at rest. Authorization codes and access and refresh tokens
are stored only as hashes. All traffic to the server uses HTTPS. Access to the production database
is restricted to the people who operate the service.

## 12. Changes to this policy

If we change this policy we will update the date at the top and publish the new version at the
same address. Material changes will also be announced through the plugin's repository.

## 13. Contact

Questions or requests: **podrska@brendly.rs**, or the contact form at
<https://brendly.rs/kontakt/>.
