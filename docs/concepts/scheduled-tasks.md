---
title: Scheduled Tasks
description: Natural language scheduling - agents that work on a schedule
---
# Scheduled Tasks

## Natural language scheduling - agents that work on a schedule


Schedule recurring tasks using natural language. Say "check my email every hour" or "remind me Monday at 9am" and agents execute those tasks automatically, on schedule.

---

## What Are Scheduled Tasks?

Scheduled tasks transform one-time requests into recurring agent actions:

```
User: "Check my email every hour for messages from my boss"
         ↓
System creates scheduled task
         ↓
Every hour: Agent checks email, reports findings
```

**The magic:** No cron syntax, no configuration files. Just natural language.

---

## How It Works

### Natural Language → Schedule

```
User: "Remind me every weekday at 2pm to review open issues"
         ↓
System understands:
  - Frequency: Every weekday
  - Time: 2pm (user's timezone)
  - Action: Review open issues
         ↓
Creates cron: 0 14 * * 1-5
         ↓
Executes automatically
```

The system parses your intent and creates the schedule automatically.

### Examples

```
"Check Slack every 30 minutes"
→ Runs every 30 minutes

"Send me a summary every Monday morning"
→ Runs Mondays at 9am (default morning time)

"Every 2 weeks on Tuesday, analyze metrics"
→ Runs every 2 weeks on Tuesday

"Daily at 5pm except weekends"
→ Runs weekdays at 5pm

"First Monday of each month at 10am"
→ Runs first Monday at 10am
```

No cron syntax needed - the agent figures it out.

---

## Creating Scheduled Tasks

### Through Conversation

Just ask:

```
User: "Check my GitHub notifications every hour"
Agent: "I've created a scheduled task to check your GitHub notifications 
        every hour. Would you like me to start it now?"
User: "Yes"
Agent: "Started! I'll check notifications every hour and notify you of 
        anything important."
```

That's it. The schedule is active.

### Verify Creation

```
User: "Show my scheduled tasks"
Agent: "You have 3 scheduled tasks:
        1. Check GitHub notifications (every hour)
        2. Daily standup summary (weekdays at 9am)
        3. Weekly metrics report (Mondays at 10am)"
```

### Modify Schedule

```
User: "Change the GitHub check to every 2 hours"
Agent: "Updated! Now checking GitHub notifications every 2 hours."
```

Natural language all the way.

---

## What Gets Scheduled

### Commands & Actions

```
"Run tests every night at midnight"
"Deploy to staging every weekday at 3am"
"Backup database daily at 2am"
"Clean up old logs every Sunday"
```

Agents execute these actions on schedule with full tool access.

### Reports & Summaries

```
"Email me a daily summary of customer feedback"
"Send weekly sales report every Friday"
"Monthly expense summary on the 1st"
"Quarterly OKR review reminder"
```

Agents gather data, analyze, and deliver reports automatically.

### Monitoring & Alerts

```
"Check API health every 5 minutes"
"Monitor server disk space hourly"
"Alert me if error rate exceeds 1%"
"Daily security scan at midnight"
```

Proactive monitoring that alerts you only when needed.

### Reminders & Notifications

```
"Remind me to review PRs every morning"
"Notify team of standup 10 minutes before"
"Send birthday wishes to customers"
"Weekly team sync reminder on Monday"
```

Never forget important tasks.

---

## Timezone Support

Schedules respect user timezones:

```
User in New York: "Check email at 9am"
→ Executes 9am ET

User in London: "Check email at 9am"
→ Executes 9am GMT
```

Each user's schedule runs in their timezone.

### Changing Timezones

```
User: "I'm traveling to Tokyo next week"
Agent: "Would you like me to adjust your scheduled tasks to Tokyo time?"
User: "Yes"
Agent: "Updated all schedules to JST timezone."
```

---

## User Isolation

Each user has their own schedules:

```
Alice: "Check my email every hour"
Bob: "Check my email every hour"

Alice's schedule: Checks Alice's email
Bob's schedule: Checks Bob's email
```

Complete isolation - no data leakage.

---

## Complex Schedules

### Exclusions

```
"Every day except holidays"
→ Skips holidays automatically

"Every weekday except company holidays"
→ Skips weekends and holidays

"Every hour except 2am-6am"
→ Runs hourly, skips overnight
```

### Conditional Execution

```
"Check Slack every hour during business hours"
→ Only runs 9am-5pm weekdays

"Send report if there's new data"
→ Only executes when data changes
```

### Dynamic Rescheduling

```
User: "If the report fails, retry in 30 minutes"
Agent: Creates schedule with retry logic
```

---

## Managing Scheduled Tasks

### List All Tasks

```
User: "What are my scheduled tasks?"
Agent: "You have 5 active schedules:
        1. GitHub notifications (every hour) - Next: 2:00pm
        2. Daily summary (9am weekdays) - Next: Tomorrow 9am
        3. Weekly report (Monday 10am) - Next: Monday 10am
        4. API health check (every 5min) - Next: 1:35pm
        5. Monthly backup (1st at midnight) - Next: Feb 1"
```

### Pause/Resume

```
User: "Pause the GitHub notifications while I'm on vacation"
Agent: "Paused GitHub notifications. Enjoy your vacation!"

[Later]
User: "Resume GitHub notifications"
Agent: "Resumed. Next check at 3pm."
```

### Delete Tasks

```
User: "Cancel the weekly report"
Agent: "Deleted weekly report schedule."
```

### Modify Tasks

```
User: "Change API health check to every 10 minutes"
Agent: "Updated API health check frequency to every 10 minutes."
```

---

## Behind the Scenes

### Cron Generation

Natural language converts to cron expressions:

```
"Every day at 9am"          → 0 9 * * *
"Every weekday at 2:30pm"   → 30 14 * * 1-5
"First Monday at 10am"      → 0 10 1-7 * 1
"Every 6 hours"             → 0 */6 * * *
```

You never see the cron - it just works.

### Intelligent Rephrasing

The system optimizes scheduled prompts:

```
User's original: "Can you check my email for messages from John?"
Scheduled prompt: "Check email for messages from John and report findings"
```

More concise, more effective for recurring execution.

### Context Preservation

Scheduled tasks maintain user context:

```
User: "Use my work email, not personal"
[Later, scheduled task runs]
Agent: [Uses work email as instructed]
```

Preferences persist across scheduled executions.

---

## Configuration

### Enable Scheduler

```yaml
# formation.afs
scheduler:
  enabled: true
  check_interval_minutes: 1    # Check for due tasks every minute
  timezone: "America/New_York" # Default timezone
```

### Database Required

Scheduled tasks require persistent storage:

```yaml
memory:
  persistent:
    enabled: true
    connection_string: ${{ secrets.POSTGRES_URI }}
    # Or: "sqlite:///./scheduler.db"
```

---

## Example Use Cases

### Development Workflow

```
"Run tests every time I push to GitHub"
"Deploy to staging every weekday at 3am"
"Send me a summary of new issues every morning"
"Remind me to review PRs every afternoon"
```

### Operations & Monitoring

```
"Check API health every 5 minutes"
"Monitor disk space every hour"
"Backup database daily at 2am"
"Weekly security scan on Sunday"
```

### Business Intelligence

```
"Daily sales summary at 8am"
"Weekly customer feedback report on Friday"
"Monthly expense analysis on the 1st"
"Quarterly revenue forecast on the 15th"
```

### Personal Productivity

```
"Remind me to stretch every 2 hours"
"Daily standup reminder 5 minutes before"
"Weekly review prompt every Friday"
"Monthly goals check-in"
```

---

## Advanced Features

### One-Time Scheduled Tasks

```
"Remind me tomorrow at 3pm to call the dentist"
→ Runs once at 3pm tomorrow, then deletes

"Send report next Monday at 10am"
→ One-time execution
```

### Chained Tasks

```
"Every night: backup database, then clean logs, then send report"
→ Executes in sequence automatically
```

### Conditional Schedules

```
"Check deployment status every 10 minutes until it succeeds"
→ Stops automatically when condition met
```

---

## Monitoring & Audit

### Execution History

```
User: "Show me the last 5 executions of my daily summary"
Agent: "Last 5 executions:
        1. Today 9:00am - Success (2 new items)
        2. Yesterday 9:00am - Success (5 new items)
        3. Monday 9:00am - Success (3 new items)
        4. Friday 9:00am - Success (7 new items)
        5. Thursday 9:00am - Failed (API timeout)"
```

### Failure Handling

When a scheduled task fails:

```
Agent: "Your daily summary failed to run at 9am due to API timeout. 
        I'll retry in 30 minutes. Would you like me to adjust the 
        retry schedule?"
```

Automatic retry with intelligent backoff.

### Success Notifications

```
User: "Notify me when scheduled tasks succeed"
Agent: "I'll send you a notification after each successful execution."

[Later]
Agent: "✓ Daily summary completed successfully. Found 3 new items."
```

---

## Why This Matters

| Without Scheduled Tasks | With Scheduled Tasks |
|------------------------|---------------------|
| Manual cron configuration | Natural language |
| Remember to check things | Agent checks for you |
| Reactive (respond to events) | Proactive (anticipate needs) |
| One-time commands | Recurring automation |
| Setup scripts and jobs | Just ask |

The result: **agents that work while you sleep**, not chatbots waiting for input.

---

## Quick Start

```yaml
# Enable scheduler
scheduler:
  enabled: true

memory:
  persistent:
    connection_string: ${{ secrets.POSTGRES_URI }}
```

Then just ask:

```
User: "Check my email every hour for urgent messages"
Agent: "Done! I'll check your email hourly and notify you of anything urgent."
```

---

## Learn More

- [Triggers & Webhooks](triggers.md) - Real-time webhook-based automation
- [Tools & MCP](tools.md) - How agents use tools in scheduled tasks
- [Memory System](memory.md) - How context persists across scheduled executions
