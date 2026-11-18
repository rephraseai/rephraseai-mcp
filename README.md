# Rephrase AI MCP Server

The official MCP server for [Rephrase AI](https://www.rephraseai.com). It lets an AI assistant rewrite text in the tone you choose while keeping the meaning the same. Ask for a line to sound more professional, more casual, shorter or more formal, and the assistant sends it to Rephrase AI and gives you back the rewrite.

This is a hosted remote server. There is nothing to download or run on your machine. You point your MCP client at the server URL and sign in with your Rephrase AI account.

```
https://www.rephraseai.com/mcp
```

Use the `www` address. The server uses Streamable HTTP and OAuth sign-in.

## What you need

- A Rephrase AI account. You can create one at [rephraseai.com](https://www.rephraseai.com).
- One of the supported clients below.

## Supported clients

### ChatGPT

1. In ChatGPT on the web, open Plugins.
2. Select the plus button, then Add custom MCP server.
3. Name it Rephrase AI. Under Connection, enter this URL: `https://www.rephraseai.com/mcp`
4. Choose OAuth, review the warning, then create it as a plugin.
5. Install the plugin. In a chat, type @ and select Rephrase AI. When asked, sign in to your Rephrase AI account.

### Cursor

Add this to `~/.cursor/mcp.json` to use it in every project, or to `.cursor/mcp.json` in a project folder to use it in that project only:

```json
{
  "mcpServers": {
    "rephrase-ai": {
      "url": "https://www.rephraseai.com/mcp"
    }
  }
}
```

Save the file, then open Customize and go to MCPs. Connect the Rephrase AI server. When asked, sign in to your Rephrase AI account.

### Gemini CLI

Install the official extension:

```
gemini extensions install https://github.com/rephraseai/gemini-cli-extension
```

The first time you use it, Gemini CLI opens your browser to the usual Rephrase AI sign-in page. Log in or create an account, approve access, and go back to the terminal. You stay signed in after that. To sign in again by hand, run this in Gemini CLI:

```
/mcp auth rephraseai
```

Claude, VS Code and other MCP clients aren't supported yet. A client that isn't on the server's allowlist can't connect, even after you sign in.

## Example

Once you're connected, ask in plain words:

```
Rephrase this in a professional tone: "hey, can we push the demo to thursday? the build isn't ready."
```

Your assistant calls Rephrase AI and shows you the rewritten text. You can name a profile, ask for a shorter or more formal version, or ask which options are available.

## Tools

### `get_rephrase_options`

Lists what you can choose from. It takes no arguments, doesn't rewrite anything and doesn't use your allowance. It returns:

- The profiles Academic, Professional, Casual, Creative and Custom, with each one's default settings.
- The use cases `school`, `workplace`, `personal` and `fiction`.
- The range for each control, from 0 to 100.
- The character limit for each plan.
- Whether SuperRephrase is available.

### `rephrase_text`

Rewrites the text you give it.

| Argument | Type | Required | What it does |
|---|---|---|---|
| `text` | string | Yes | The text to rewrite, from 1 to 10,000 characters. It can't be only spaces or blank lines. Free accounts can send up to 3,000. |
| `profile` | `Academic`, `Professional`, `Casual`, `Creative` or `Custom` | No | Starts from that profile's default settings. Without it, every control starts at 50. |
| `formality` | number, 0 to 100 | No | How formal the rewrite sounds. Higher is more formal. |
| `complexity` | number, 0 to 100 | No | How advanced the vocabulary and sentences are. Higher is more complex. |
| `brevity` | number, 0 to 100 | No | How much the rewrite shortens the text. Higher is shorter. |
| `creativity` | number, 0 to 100 | No | How freely the wording can change. Higher allows bigger changes. |
| `useCase` | `school`, `workplace`, `personal` or `fiction` | No | Tells Rephrase AI where the text will be used. |
| `superrephrase` | boolean | No | Reworks sentence structure and rhythm more thoroughly. Pro accounts only. |

Any control you send overrides the chosen profile's default for that control. Any control you leave out keeps the profile's default.

A successful call returns `rephrasedText`, the `profile` used, `savedToHistory`, and, when available, your `usage`.

## Usage and history

Each successful rewrite uses one rewrite from your Rephrase AI plan, the same allowance you use on the Rephrase AI website and in other apps. The original text, the rewrite and the settings are saved to your Rephrase AI History. Listing options doesn't count toward your plan.

Free accounts can send up to 3,000 characters per request, and Pro accounts up to 10,000. SuperRephrase is Pro only.

## Errors

| Code | Meaning |
|---|---|
| `REPHRASE_USAGE_LIMIT` | You've used all the rewrites in your plan for now. |
| `REPHRASE_CHARACTER_LIMIT` | The text is longer than your plan allows per request. |
| `REPHRASE_PRO_FEATURE` | You asked for SuperRephrase on a Free account. |
| `ACCOUNT_UNAVAILABLE` | Your Rephrase AI account has no email address yet. Add one to your account, then try again. |
| `REPHRASE_FAILED` | The rewrite didn't complete and no more specific code applies, for example a subscription or saving problem. Try again, and contact support if it keeps happening. |
| `RATE_LIMIT` | Too many rewrite requests in a short time. The limit is 10 a minute. Wait a moment and try again. |

If your client gets HTTP 401, it isn't signed in, so connect your Rephrase AI account again. HTTP 403 means the client isn't on the supported list.

## For client developers

- Endpoint: `https://www.rephraseai.com/mcp`, which uses Streamable HTTP and answers each request with a JSON response. There are no session IDs.
- Discovery: `https://www.rephraseai.com/.well-known/mcp.json`
- Protected resource metadata: `https://www.rephraseai.com/.well-known/oauth-protected-resource/mcp`
- Authorization server: `https://clerk.rephraseai.com`. Its metadata is at `https://clerk.rephraseai.com/.well-known/oauth-authorization-server`.
- Scope: `rephrase:write`, plus `offline_access` for refresh tokens.
- Client registration: Client ID Metadata Documents are supported. Dynamic Client Registration isn't. New clients have to be added to the server's allowlist before they can connect.

## Help

Questions or problems? Visit [Rephrase AI support](https://www.rephraseai.com/support). You can create an account or learn more at [rephraseai.com](https://www.rephraseai.com).
