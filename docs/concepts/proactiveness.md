---
title: Proactiveness
description: Let a formation initiate contact - notifications, heartbeats, channels, and slash commands
---

# Proactiveness

## Formations that reach out, not just respond

By default a formation only answers when spoken to. The `proactive:` block lets
it initiate contact: deliver notifications, run periodic heartbeats, and address
users on the channel they last used. Everything here is inert until configured.


## Channels are transformers

A **channel** is a named reference to a [trigger transformer](../reference/triggers.md),
so outbound delivery reuses the same template substitution, auth, and retry
machinery, with a single webhook fallback when every channel fails.

Bundled dormant templates ship for `slack`, `telegram`, `discord`, and `email`
(real platform payload shapes, no URLs, inert until referenced, shadowed by a
formation-local `transformers/` file). Email emits a constructed message object
(from/to/subject/body/headers) to your bridge webhook; SMTP/SES wiring stays
your side.

Each channel accepts only two keys: `transformer` (required) and an optional
`url` (an http(s) URL or a `${{ secrets.* }}` template) that overrides the
transformer's own endpoint.

```yaml
proactive:
  channels:
    slack:
      transformer: slack
      url: "${{ secrets.SLACK_WEBHOOK }}"
  default_channel: slack        # a channel name or "webhook"
```


## Routing precedence

Outbound delivery resolves in this order: explicit channel(s) → user preference
→ formation default (`default_channel`) → webhook. The reserved targets `last`,
`preferred`, and `webhook` and multi-channel arrays are supported. Per-user channel state
(preferred channel, addressing context, last channel, timezone) is kept in
memory with write-through persistence and exposed via:

| Endpoint | Purpose |
|----------|---------|
| `POST /v1/notifications` | Send a notification through the routing chain |
| `GET /v1/users/{id}/channels` | Read a user's channel state |
| `PUT /v1/users/{id}/channels` | Update a user's channel state |

Inbound chat and triggers record which channel a user last spoke on, so "reply
where they are" works.


## Heartbeat

A heartbeat rides the existing scheduler through a periodic-task extension point:
interval gating, active hours (fixed or user-timezone, overnight ranges, weekend
flags), and a fresh session per run with full failure isolation.

```yaml
proactive:
  heartbeat:
    enabled: true                # default true when the block is present
    interval: "30m"              # <N>s / <N>m / <N>h (optional "every " prefix)
    target: "last"               # last | preferred | webhook | <channel>
    active_hours:                # optional; absent means always active
      start: "08:00"
      end: "20:00"
      timezone: "user"           # IANA name, or "user" for per-user tz
      weekends: true             # false suppresses Saturday/Sunday
    instruction: "..."           # optional extra prompt content
    sop: my-heartbeat            # optional SOP name for the base prompt
```

A heartbeat enabled with no `sop:` / `instruction:` falls back to a bundled
default SOP (check due jobs and recent context, reach out only when warranted,
otherwise emit `HEARTBEAT_OK`). The `HEARTBEAT_OK` sentinel suppresses delivery
and is detected even when synthesis layers wrap it - scoped to heartbeat
sessions, so a normal chat that mentions it is untouched.


## Soul documents

The overlord's persona can live in a `SOUL.md` / `soul.md` next to
`formation.yaml`, auto-discovered as the default persona. Precedence: `SOUL.md`
> `soul.md` > inline `overlord.soul` > built-in default. Agents stay
single-file: an agent's character lives in its `system_message`.


## Slash commands

An opt-in `commands:` block maps `/command` to SOPs by explicit invocation (no
LLM round-trip for unknown commands). Nine fully deterministic built-in
commands are active whenever the block is enabled:

| Command | Purpose |
|---------|---------|
| `/setup` | Guided per-user setup flow derived from the formation's own config |
| `/jobs` | List/pause/resume/cancel/logs for the caller's scheduled jobs |
| `/identity` | Link/unlink identifiers (cross-user protected) |
| `/channels` | Read/write channel state (`/channels test` sends a real notification) |
| `/preferences` | Read/write per-user preferences |
| `/reset` | Clear the current session buffer |
| `/help` | List available commands |
| `/status` | Read formation status |
| `/learnings` | Review, apply, or dismiss pending self-tuning lessons |

Formation-defined commands shadow built-ins (author control wins), and a
`commands.builtin:` map disables individual built-ins.

```yaml
commands:
  enabled: true
  aliases:
    tasks: jobs
  builtin:
    reset: false
```

The feature is inert when `commands:` is absent. With the block present,
`enabled` defaults to `true`.


## Learn More

- [Scheduled Tasks](scheduled-tasks.md) - the scheduler heartbeats ride on
- [Triggers & Webhooks](triggers-and-webhooks.md) - transformers power channels
- [Overlord Soul](soul.md) - the persona a SOUL.md document defines
