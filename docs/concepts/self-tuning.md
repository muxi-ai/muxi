---
title: Self-Tuning
description: How a formation learns from its own activity and improves over time
---

# Self-Tuning

## Formations that learn from how they're used

A MUXI formation observes its own activity and periodically distills what it
learns into durable behavioral guidance. That guidance lives in a file called
`MUXI.md` at the formation root - the formation's "captain's log" of learned
behavior - and it steers the overlord and agents on every subsequent turn.

Self-tuning is **on by default**. Every formation improves out of the box; you
can dial it down or turn it off with the `tuning:` block.


## How the loop works

A single in-runtime job runs on a schedule (once a day by default) and makes two
passes:

1. **Digest** - it reads the event spool accumulated since the last checkpoint
   and aggregates it into a compact activity report, then checkpoints. The
   digest is also stored as a formation-scope Captain's Log entry and injected
   into every user's context as the "Formation operations log."
2. **Tune** - it reads that digest, recent log entries, and past experiments,
   detects patterns, and distills behavioral learnings into a candidate revision
   of `MUXI.md`. Learnings whose watched metric never moved are retired. A short
   "morning report" summarizes what changed.

Depending on `auto_apply`, a candidate revision is either applied directly to
`MUXI.md` or written alongside it as `PENDING-MUXI.md` for you to review.


## Configuration

The top-level `tuning:` block controls the loop. An absent block means "on with
defaults":

```yaml
tuning:
  active: true            # Off switch: false disables the loop entirely
  interval_hours: 24      # How often the loop runs (default: 24)
  auto_apply: true        # Apply revisions directly, or hold them as pending
```

Shorthands:

```yaml
tuning: false             # Same as { active: false }
```

| Field | Default | Description |
|-------|---------|-------------|
| `active` | `true` | Whether the tuning loop runs at all |
| `interval_hours` | `24` | Hours between loop passes (must be positive) |
| `auto_apply` | `true` | `true` applies revisions to `MUXI.md`; `false` writes `PENDING-MUXI.md` for review |

Invalid values fail fast at formation load, never at tuning time.


## The tuning files

| File | Role |
|------|------|
| `MUXI.md` | The live, applied learnings - read on every turn |
| `PENDING-MUXI.md` | A candidate revision awaiting your review (only when `auto_apply: false`) |

Both files are **runtime-owned state**. When you redeploy or roll back a
formation, the server preserves the live `MUXI.md` and `PENDING-MUXI.md` (just
like `memory.db`) so an update never wipes what the formation has learned. See
[Managing Formations](../server/managing-formations.md#preserved-runtime-state).


## Reviewing suggestions from the CLI

When `auto_apply` is off, the CLI lets you inspect and act on the pending
suggestion:

```bash
# Review the live and pending state
muxi tuning show        # Display the live MUXI.md
muxi tuning pending     # Display the pending suggestion, if any

# Act on the pending suggestion
muxi tuning apply       # Promote the pending suggestion to the live MUXI.md
muxi tuning dismiss     # Discard it (dismissed learnings are never re-proposed)
```

The morning report can also carry an apply/dismiss widget, so a review can
happen inline in a chat channel as well as from the CLI.

When the `commands:` block is enabled, the deterministic `/learnings` command
provides the same chat surface:

```text
/learnings
/learnings pending
/learnings apply
/learnings dismiss
```

`apply` and `dismiss` are refused in multi-user mode because they change
formation-wide guidance; operators must use the admin tuning API instead.


## API surface

The same state is available over the admin API (all paths under `/v1`, admin
key required):

| Endpoint | Purpose |
|----------|---------|
| `GET /v1/tuning` | Read the live `MUXI.md` |
| `POST /v1/tuning` | Replace the live file (human upload) |
| `POST /v1/tuning/run` | Trigger one tuning loop pass |
| `GET /v1/tuning/pending` | Read the pending suggestion (null when none) |
| `PATCH /v1/tuning/pending` | Accept: promote pending to live |
| `DELETE /v1/tuning/pending` | Dismiss: discard the suggestion |

Applied learnings are injected into the overlord's context via the captain's-log
layer, so guidance steers every turn without re-reading the whole file.


## Benchmark-driven observation

When `MUXI_BENCH_ROOT` is set, the tuning loop also observes benchmark runs
rooted there, folding their outcomes into the digest so learnings can be
correlated against reproducible benchmark metrics rather than live traffic
alone.


## Learn More

- [Managing Formations](../server/managing-formations.md) - how tuning state survives updates
- [Formation Schema](../reference/formation-schema.md) - the full configuration surface
- [Observability](observability.md) - the event spool the digest reads from
