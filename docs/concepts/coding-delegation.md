---
title: Coding-Agent Delegation
description: Delegate coding tasks to external headless coding CLIs as background work
---

# Coding-Agent Delegation

## Hand coding tasks to an external CLI

A formation can delegate coding work to an external headless coding CLI
(claude-code, droid, opencode, pi, or your own adapter) as fire-and-collect
background jobs. The agent calls one tool; MUXI runs the CLI in an isolated
working directory, tracks the job, and re-enters the conversation with the
result when it finishes.


## The `coding:` block

Declare an adapter once at the top level. With no `coding:` block, nothing is
constructed and no tool is registered.

```yaml
coding:
  client: droid                 # a bundled adapter template
  model: claude-sonnet-4-5      # opaque; per-call override allowed
  workdirs:
    - ./workspaces               # each job runs in <root>/<user_id>/<request_id>
  cleanup: delete                # delete (default, TTL sweep for strays) | keep
  timeout: 30m
  max_concurrent: 3              # per user
  env:                           # the ONLY place ${{ secrets.* }} resolves
    ANTHROPIC_API_KEY: "${{ secrets.ANTHROPIC_API_KEY }}"
```

Instead of `client`, you can inline an adapter with a `command`, structured
`args` fragments containing `{prompt}`, `{id}`, and `{model}` slots, an
`output` mode (`stream-json`, `json`, or `text`), and optional `parse.result`
and `parse.session_id` selectors. A resource-side `groups:` allowlist,
`extra_args` vendor passthrough, and per-call `model` override are supported.
Named templates own their adapter shape, so do not combine `client` with the
inline keys `command`, `args`, `output`, or `parse`.

Load-time validation is fail-fast: binary presence, adapter schema, workdir
roots, group existence (when RBAC is active), and the output/cleanup/timeout
enums. `${{ secrets.* }}` referenced anywhere outside `env:` fails the load.

### Bundled adapters

| Adapter | Notes |
|---------|-------|
| `claude-code` | Session via fresh id (create-or-resume) |
| `droid` | `--session-id` + `--output-format json` |
| `opencode` | `opencode run --format json`; captured session id |
| `pi` | `pi --print --mode json`; captured session id |

Templates are formation-local shadowable, with an inline escape hatch.


## The `delegate_coding` tool

When a `coding:` block exists, agents get a `delegate_coding` built-in tool. It
is **always asynchronous**: it returns a job id immediately.

Parameters: `prompt`, `workdir`, `model`, and `continue_job_id` (session
continuation - vendor session ids persist on the job record and never reach
agents). Allowlist, concurrency, and unknown-job rejections come back as
friendly error dicts, never a crashed turn.


## Managing jobs

Delegations are tracked background jobs, visible via the scheduler `/jobs`
surface (list, cancel, logs). Cancelling kills the process group but keeps the
session resumable. Completion re-enters the conversation through the middleware
+ RBAC pipeline (`route_class: delegation`) and is delivered via the
[proactiveness](proactiveness.md) notification router.


## Learn More

- [Tools & MCP](tools-and-mcp.md) - other built-in tools
- [Access Control](access-control.md) - the `groups:` allowlist and re-entry pipeline
- [Async Processing](async.md) - background jobs and re-entry
