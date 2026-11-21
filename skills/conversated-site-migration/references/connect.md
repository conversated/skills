# Connect the Conversated MCP

Read this when the session has no Conversated admin tools. The owner does the steps in their admin and their AI client. You guide, then verify.

## What to connect to

Each site has its own MCP server at `https://<access-domain>/_/mcp`. The exact URL is shown in the site's admin under **AI & LLMs > MCP > Setup Guide** (`/_/ai/mcp`). It lives on the site's Access Domain, the address the owner uses to sign in to the admin, not on the public marketing domain.

`/mcp` without the `/_` prefix is the visitor endpoint. It offers visitor tools such as search and cannot edit the site. Seeing only those tools means the wrong URL is connected.

Only an Account Owner or Admin can connect. Staff and Member roles cannot approve access.

## Pick the method for the client

| Client                                                                                           | Method                                  | Owner's steps                                                                                                                                                                                                                                                                      |
| ------------------------------------------------------------------------------------------------ | --------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Apps that add a server by its URL, such as ChatGPT (**Plugins**, then **Add custom MCP server**) | Sign-in (OAuth)                         | Paste the **MCP Server URL** and choose sign-in (OAuth) as the authentication. Sign in on the Access Domain as Owner or Admin, then review and approve the consent page. No key is needed. Menu names change between app versions, so look for the option that takes a server URL. |
| Claude Code                                                                                      | API key                                 | Generate a named key under **API Keys**. In a terminal, run the ready-made command from the Setup Guide: `claude mcp add --transport http <name> <mcp-url> --header "Authorization: Bearer <key>"`. Pasting the supplied snippet into `.mcp.json` works as well.                   |
| Claude Desktop config file                                                                       | API key through the `mcp-remote` bridge | Generate a key, merge the **Claude Desktop Config** snippet into `claude_desktop_config.json`, then fully restart the app. A bare URL entry is rejected there.                                                                                                                     |
| Any other MCP client                                                                             | Whichever it supports                   | Sign-in when the client can open a browser window; otherwise a Bearer key in an `Authorization` header.                                                                                                                                                                            |

Sign-in and API keys expose the same tools for the same site.

## Handle keys safely

- A new key is shown once. The owner copies it straight into their client's configuration.
- Keep keys out of the conversation and out of every file you write: plans, briefs, packet files and logs.
- When the configuration sits in a project file such as `.mcp.json`, keep that file out of version control.
- Suggest one key per client or teammate, and revoking a key made only for this migration once the work is accepted.

## Start a connected session

Most clients load a new server only at startup. After connecting, the owner restarts the client or opens a new session. In Claude Code, `/mcp` lists the server and confirms it connected.

Before the restart, save what you already know to `conversated-migration/intake.md`: the owner's request, the source, the target site and any answers so far. The new session starts from that file instead of asking again. In a chat-only client, give the owner the same notes as text to paste into the new conversation.

Give the owner a line to paste into the new session, and make sure this skill reaches that session the same way it reached this one:

> Use the conversated-site-migration skill to continue the migration in `conversated-migration/intake.md`.

## Verify before any work

1. `get_site_info` describes the expected site.
2. `list_pages_admin` returns the site's Pages, which proves admin access.
3. When you plan to delegate, one worker calls `get_site_info` and `list_pages_admin` successfully, which proves workers reach the same admin connection.

One connection serves one site. Name each connection after its site when the owner has several.

## When it does not work

| Symptom                                                   | Likely cause                                                                   | Fix                                                                                                        |
| --------------------------------------------------------- | ------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------- |
| Authentication error                                      | Key mistyped or revoked, or its creator is no longer an Owner or Admin         | Generate a new key under **API Keys** and confirm the role                                                 |
| Only visitor tools appear                                 | The client points at `/mcp`                                                    | Use the `/_/mcp` URL from the Setup Guide                                                                  |
| `get_site_info` describes another site                    | The URL came from a different site's admin                                     | Copy the URL from the target site's Setup Guide                                                            |
| Sign-in is refused or loops                               | Signed in with a Staff or Member account, or on a different domain             | Sign in on the Access Domain as Owner or Admin and complete any two-factor step                            |
| A connection that worked is now refused                   | The sign-in grant was revoked or is no longer valid                            | Reconnect once and approve again                                                                           |
| A tool this skill names is missing                        | Its module is not enabled on this site                                         | Plan with the tools the server lists, and raise the missing capability as an owner decision                |
| Tools that worked earlier are missing now                 | The app attaches a connection to one message at a time                         | Ask the owner to select the connection again in their next message. In ChatGPT they type `@` and choose it |
| Read tools answer, but every change is refused or missing | The app, or the owner's plan in it, allows only read access to a custom server | Tell the owner. Intake and the plan can continue; execution needs a plan or an app that allows changes     |
