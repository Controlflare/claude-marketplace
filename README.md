# Controlflare plugins for Claude Code

A [Claude Code plugin marketplace](https://docs.claude.com/en/docs/claude-code/plugins) holding one
plugin: **Controlflare**, a hard monthly billing cap for Cloudflare Workers.

The plugin is a thin thing. It contains no code and runs no local process: it
points Claude at Controlflare's hosted MCP server, and everything happens
there. What you are installing is three JSON files.

## Install

```
/plugin marketplace add Controlflare/claude-marketplace
/plugin install controlflare@controlflare
```

Or in one command, from a shell:

```
claude plugin install controlflare --marketplace Controlflare/claude-marketplace
```

There is nothing else to configure. No API key, no token, no config file to
edit. The first time Claude calls a tool, Controlflare answers `401` with the
address of its sign-in, Claude registers itself, and your browser opens a
consent page that names the client and lists what it will be allowed to do.
You approve it there, sign in with Cloudflare, and Claude holds a token from
then on. Revoke it by removing the plugin, or from the Controlflare dashboard.

## What Claude can do with it

Twelve tools, of three kinds.

**Reading (five).** `list_cloudflare_accounts`, `get_account`,
`get_estimated_spend`, `list_workloads`, `list_events`.

**Changing a setting (three).** `set_billing_cap` sets or clears the hard
monthly limit in USD, `set_enforcement` turns automatic pausing on or off, and
`sync_account` takes a fresh reading from Cloudflare now.

**Stopping traffic (four).** `pause_workload`, `resume_workload`,
`pause_all_workloads`, `resume_all_workloads`.

## Read this part before you install it

**Four of these tools stop production traffic.** Pausing a Worker removes its
workers.dev subdomain, its custom domains, its zone routes and its cron
schedules. Requests to it stop being served. Resuming puts all of that back,
but the outage in between was real.

`set_billing_cap` is in the same category by a slower route: setting a cap at
or below what an account has already spent this month will pause every running
Worker on it at the next check.

Claude marks those four as destructive and will ask before running them. Do not
turn that off for this server. If you want an account Claude cannot stop, sign
in as a user who does not have it.

Nothing here is an invoice. Controlflare estimates spend from Cloudflare's
usage analytics priced at published rates, and Cloudflare bills you, not us.
See the [disclaimer](https://controlflare.com/disclaimer/).

## Self-hosting

If you run your own Controlflare instance, the hosted URL is wrong for you.
Fork this repo and change the one line in
`plugins/controlflare/.mcp.json`:

```json
{
  "mcpServers": {
    "controlflare": {
      "type": "http",
      "url": "https://controlflare.example.com/mcp"
    }
  }
}
```

The MCP server needs a KV namespace and Cloudflare sign-in configured; a
self-hosted instance without them answers `404` on `/mcp`. See
[SELF_HOSTING.md](https://github.com/whirlwin/controlflare/blob/main/docs/SELF_HOSTING.md).

## Links

- [Controlflare for AI agents](https://controlflare.com/agents/), the full MCP documentation
- [controlflare.com](https://controlflare.com)
