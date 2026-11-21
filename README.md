# Conversated skills

Skills that teach your own AI agent how to work on your Conversated site. Your agent connects to the site through the site's MCP server and follows the skill, so the work runs in your agent, under your account.

| Skill | What it does |
| --- | --- |
| [conversated-site-migration](skills/conversated-site-migration/SKILL.md) | Moves an existing website or a finished design onto a Conversated site. It plans the whole migration, asks you for one approval, then builds the pages. |

**Status: preview.** On your first run, choose "drafts only" when the agent asks how far to go. Nothing goes live until you have reviewed it.

## What you need

- A Conversated site where you are an Owner or Admin.
- An AI agent that supports skills and MCP servers: ChatGPT, Codex, Claude Code, the Claude apps, or any agent that reads the [Agent Skills](https://agentskills.io) format.

## 1. Install the skill

### ChatGPT

1. Download `conversated-site-migration.zip` from the [latest release](https://github.com/conversated/skills/releases/latest).
2. In ChatGPT on the web, open **Plugins** and select the **Skills** tab. Select **Create**, then **Upload from your computer**, and choose the zip.

A workspace admin can instead import this repository as a plugin marketplace under **Admin > Plugins**. ChatGPT then keeps the skills up to date for the whole workspace.

OpenAI decides which ChatGPT plans can upload skills and add custom MCP servers. If you cannot find these options, see OpenAI's help pages on [skills](https://help.openai.com/en/articles/20001066-skills-in-chatgpt) and [custom MCP servers](https://help.openai.com/en/articles/12584461-connecting-custom-mcp-servers-in-chatgpt), or use Codex.

### Codex

```bash
codex plugin marketplace add conversated/skills
codex plugin add conversated@conversated-skills
```

### Claude Code

```text
/plugin marketplace add conversated/skills
/plugin install conversated@conversated-skills
```

### Claude desktop and web apps

Download `conversated-site-migration.zip` from the [latest release](https://github.com/conversated/skills/releases/latest) and upload it as a skill in the app's settings.

### Any other agent

Copy the folder `skills/conversated-site-migration` into the place your agent loads skills from.

## 2. Connect your site

Your site's MCP server address is in its admin, under **AI & LLMs > MCP > Setup Guide**. It ends in `/_/mcp`. Only an Owner or Admin can connect.

- **ChatGPT.** On the web, open **Plugins**, select **+**, then **Add custom MCP server**. Paste the address, choose OAuth as the authentication, and sign in to your site. To use it in a chat, type `@` and pick it. ChatGPT applies that choice to one message at a time, so pick it again whenever you want the agent to act on your site.
- **Codex.** Run `codex mcp add my-site --url https://<your-site>/_/mcp`, then `codex mcp login my-site`.
- **Claude Code and Claude Desktop.** Follow the Setup Guide. It gives you a ready-made command or configuration that uses a key from the **API Keys** tab.

If the site is not connected when you start, the skill walks your agent through these steps.

## 3. Run a migration

Start a new session and say:

> Use the conversated-site-migration skill to migrate [your current site or your design] onto my Conversated site.

The agent asks you once for what it needs, shows you a plan to approve, then builds.

## What it costs

- Your agent runs on your own AI subscription.
- Importing images spends your site's AI wallet. The plan tells you how many images it intends to import before it starts.

## Feedback

Open an issue in this repository and say which agent you used. Issues are public, so leave out API keys and anything private about your site.

## License

Licensed under the [PolyForm Shield License 1.0.0](LICENSE.txt).

Required Notice: Copyright Spacethree LLC dba Humble Good
