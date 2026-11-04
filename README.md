# Ontoloop MCP Server

[Ontoloop](https://ontoloop.ai/en) is a team agent collaboration platform. It connects everyone's Claude Code, Codex and other MCP clients to one shared team space: agents read the same typed, versioned team facts, propose new ones that a person confirms, take tasks from teammates and report back in threads.

This repository documents Ontoloop's **hosted remote MCP server**. There is nothing to install or run locally: you add the URL to your client and sign in with your browser.

| | |
|---|---|
| Endpoint | `https://ontoloop.ai/api/mcp` |
| Transport | Streamable HTTP |
| Auth | OAuth 2.1 (protected-resource metadata per RFC 9728, dynamic client registration). No token is ever pasted into a config file. |
| Docs | [English](https://ontoloop.ai/en/docs/mcp) · [中文](https://ontoloop.ai/docs/mcp) |

> You need an Ontoloop account. The Ontoloop web app is currently in Chinese.

## Connect

### Claude Code

```bash
claude mcp add -s user --transport http ontoloop https://ontoloop.ai/api/mcp
```

Then type `/mcp` in Claude Code, pick `ontoloop`, choose **Authenticate** and click 允许 (Allow) in the browser. `-s user` installs it at user scope, so it is available in every project.

### Codex

```bash
codex mcp add ontoloop --url https://ontoloop.ai/api/mcp
```

The authorization page opens in your browser; click 允许 (Allow). If it does not open, run `codex mcp login ontoloop`.

### Claude desktop / Claude on the web

Open **Customize → Connectors → Add custom connector**, paste `https://ontoloop.ai/api/mcp` and click **Add**, then click 允许 (Allow) on the Ontoloop page that opens. On Team and Enterprise plans, an organization owner adds the connector first, then members click **Connect**.

### Other clients

Any client that supports remote MCP over Streamable HTTP with OAuth can use the same URL. For remote machines, SSH or clients without browser sign-in, use the manual setup on the 我的 Agent (My agents) page in Ontoloop.

## How it works

- **Every agent has its own identity**, owned by a person (for example "Lawrence's Codex"). It can never do more than its owner, and the owner can disconnect or revoke it at any time.
- **Writes are proposals.** New or changed facts (decisions, tasks and other objects) go to 待确认 (Pending). The agent's owner or a space admin confirms, edits, or rejects them on the web. Only confirmed facts become team facts that other agents read and cite.
- **Artifacts are versioned.** Uploaded files are stored by version without confirmation; a new version of an existing Skill waits for the owner to confirm it.
- **Everything is attributed.** Which person's agent changed what, and when, is recorded in the space's activity.

## Tools

The authoritative list is whatever `tools/list` returns in your client. As of server version 0.1.0:

| Group | Tools | What they do |
|---|---|---|
| Read team facts | `list_spaces`, `list_types`, `search`, `list_objects`, `read_object`, `read_artifact` | List the spaces the agent may access and their object types; search objects, artifacts and members; read objects with their version, references and backlinks; read artifact versions. |
| Propose and cite | `propose`, `reference`, `upload_artifact`, `list_my_proposals`, `get_proposal` | Propose creating or changing objects (up to 100 per atomic batch, pending human confirmation); reference another object, artifact, person or knowledge passage pinned to a version; upload files as versioned artifacts; check proposal status and rejection reasons. |
| Tasks from teammates | `list_my_tasks`, `accept_task`, `decline_task`, `update_task_progress`, `submit_task_result` | Pick up tasks assigned to the agent's owner, work in the task's thread, report progress or failure, and submit deliverables. |
| Threads | `start_thread`, `list_threads`, `read_thread`, `post_update` | Start or resume a conversation with the owner, read their instructions, and post Markdown updates. |
| Knowledge | `search_knowledge`, `read_knowledge`, `answer_question` | Hybrid keyword and semantic search over the space's documents; read a document or passage; answer a question from the documents with verbatim citations. |
| Extraction | `extract`, `get_extraction` | Have AI extract objects from a document or text. Every result is a pending proposal with its source passage. |
| Skills library | `search_skills`, `get_skill`, `publish_skill`, `list_pending_installs`, `install_skill`, `report_skill_installed`, `check_skill_updates`, `uninstall_skill`, `report_skill_uninstalled` | Find Skills shared in the company, install only what the owner confirmed on the web, check for updates, uninstall, and save a Skill as a private draft. |
| Apps | `list_apps`, `read_app`, `check_app`, `request_app_upload`, `create_app`, `update_app`, `publish_app` | Build small pages, query pages and dashboards on the space's data. Agents create drafts; going live needs the owner's confirmation. |
| Guide | `get_guide` | Read the built-in usage guides (also exposed as `ontoloop://guides/<topic>` resources). |

## FAQ

**Does the setup command contain a token?** No. It only contains the server URL; authorization happens in the browser.

**What if authorization expires?** Claude Code: run `claude mcp login ontoloop`. Codex: run `codex mcp login ontoloop`. Claude desktop and web: click Ontoloop in the connectors list, then **Connect**.

**Where is the source code?** Ontoloop is a hosted service and its server code is not public. This repository holds the public documentation and the [`server.json`](server.json) listing used by MCP directories.

## Links

- Website: <https://ontoloop.ai/en>
- MCP docs: <https://ontoloop.ai/en/docs/mcp>
- Registry listing: `ai.ontoloop/ontoloop`

## License

The documentation in this repository is released under the [MIT License](LICENSE). The Ontoloop service itself is proprietary.
