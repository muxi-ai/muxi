# Domain Expert Review Checklist

**Generated:** 2026-01-10
**Purpose:** Items requiring domain expert verification before docs can be considered 100% accurate


## Priority 1: API Behavior vs Documentation

### 1.1 Session Creation Endpoint
**File:** `sdks/python-sdk.md`, `sdks/typescript-sdk.md`, `reference/api-reference.md`

**Issue:** Docs show `POST /sessions` to create sessions, but runtime returns 405 Method Not Allowed.

**Observed behavior:** Sessions are auto-created on first chat message.

**Questions:**
- [ ] Is explicit session creation supported? If not, update SDK docs.
- [ ] Should docs clarify sessions are auto-created?

---

### 1.2 Agent Response Fields
**File:** `reference/agents.md`, `reference/formation-schema.md`

**Observed in runtime response:**
```json
{
  "role": "generalist",
  "source": "formation",
  "specialties": []
}
```

**Questions:**
- [ ] `role: "generalist"` - Is this correct? Schema shows `role: "specialist"` as example
- [ ] `source: "formation"` - Not documented. What values are possible?
- [ ] `specialties: []` - Empty array shown. How are specialties populated?

---

### 1.3 Chat Response Event Types
**File:** `reference/api-reference.md`, `deep-dives/real-time-streaming.md`

**Observed SSE event types:**
- `progress` - General progress updates
- `thinking` - "Understanding the user's request..."
- `planning` - "Determining the best agent..."
- `completed` - Final response

**Questions:**
- [ ] Are all event types documented?
- [ ] Is the event schema (`token.type`, `token.stage`, etc.) documented?
- [ ] What other event types exist? (`error`, `tool_call`, etc.?)


## Priority 2: Feature Implementation Verification

### 2.1 A2A Protocol
**Files:** `concepts/agents-and-orchestration.md` (A2A section), `deep-dives/how-orchestration-works.md`

**Questions:**
- [ ] Is cross-formation A2A actually implemented?
- [ ] Is the `a2a:` configuration in formation.afs functional?
- [ ] Are the security claims (nonce signing, hop limits) implemented?

---

### 2.2 Artifact Generation
**File:** `concepts/artifacts.md`

**Claims to verify:**
- [ ] Sandboxed Python execution works as described
- [ ] Blocked imports list is accurate (subprocess, os.system, etc.)
- [ ] File size limits enforced
- [ ] MIME type detection works

---

### 2.3 Scheduled Tasks
**File:** `concepts/scheduled-tasks.md`

**Claims to verify:**
- [ ] Natural language schedule parsing works ("every Monday at 9am")
- [ ] Timezone handling is correct
- [ ] PostgreSQL required for persistent schedules?
- [ ] Cron syntax generation is accurate

---

### 2.4 Clarification System
**File:** `concepts/clarification.md`

**Claims to verify:**
- [ ] Multi-credential detection works (shows "You have 2 GitHub accounts...")
- [ ] Context switch detection works
- [ ] `clarification.style` setting affects output
- [ ] `max_turns` is enforced

---

### 2.5 User Synopsis Caching
**File:** `concepts/memory-system.md`

**Claims to verify:**
- [ ] Synopsis is actually LLM-generated
- [ ] Two-tier caching (identity + context) works as described
- [ ] `cache_ttl` setting is respected
- [ ] "80% token reduction" claim is realistic


## Priority 3: Configuration Accuracy

### 3.1 Formation Schema Fields
**File:** `reference/formation-schema.md`, `concepts/formation-schema.md`

**Questions:**
- [ ] Is `overlord.workflow.complexity_threshold` actually used? (default 7.0)
- [ ] Is `overlord.workflow.plan_approval_threshold` functional?
- [ ] Does `async.threshold_seconds` trigger automatic async mode?
- [ ] Are all `llm.models` capability types supported? (text, embedding, vision, audio, streaming)

---

### 3.2 Memory Configuration
**File:** `concepts/memory-system.md`, `concepts/multi-tenancy.md`

**Questions:**
- [ ] Does SQLite work for single-user? (docs say PostgreSQL required for multi-tenant)
- [ ] Is `memory.working.mode: "remote"` (FAISSx) functional?
- [ ] Is buffer memory truly namespaced by user_id automatically?

---

### 3.3 MCP Configuration
**File:** `concepts/tools-and-mcp.md`, `reference/tools.md`

**Questions:**
- [ ] Does `type: http` MCP work? (vs `type: command`)
- [ ] Is `${{ user.credentials.* }}` interpolation working?
- [ ] Are timeout and retry settings respected?


## Priority 4: Security Claims

### 4.1 Secrets Encryption
**File:** `concepts/secrets-and-security.md`, `deep-dives/security-model.md`

**Claims to verify:**
- [ ] AES-256-GCM encryption is used
- [ ] PBKDF2 key derivation is used
- [ ] Secrets never appear in process environment
- [ ] Secrets never logged

---

### 4.2 Multi-Tenancy Isolation
**File:** `concepts/multi-tenancy.md`, `deep-dives/multi-tenancy.md`

**Claims to verify:**
- [ ] PostgreSQL RLS policies are created automatically
- [ ] Cross-user session access returns 404 (not 403)
- [ ] Buffer memory is truly isolated per user

---

### 4.3 HMAC Authentication
**File:** `deep-dives/security-model.md`, `server/authentication.md`

**Claims to verify:**
- [ ] 5-minute timestamp window is enforced
- [ ] Body hash is included in signature
- [ ] Replay attacks are blocked


## Priority 5: Minor Clarifications Needed

### 5.1 Terminology Consistency
- [ ] "Formation" vs "formation" - when to capitalize?
- [ ] "Overlord" vs "overlord" - when to capitalize?
- [ ] ".afs" vs ".yaml" - are both truly equivalent everywhere?

### 5.2 Version Claims
**File:** `concepts/versioning.md`

- [ ] Are migration guides at docs.muxi.org/migrations/* actually published?
- [ ] Does `runtime.auto_download: true` work?

### 5.3 Registry Claims
**File:** `concepts/registry.md`

- [ ] Is registry.muxi.org live?
- [ ] Does `muxi pull @muxi/starter` work?
- [ ] Are private registries actually "planned"?


## How to Use This Document

1. Go through each checkbox
2. Mark ✅ if verified correct, ❌ if incorrect
3. For incorrect items, note what needs to change
4. Delete this file when review is complete


## After Review

Once verified, update confidence to **98-100%** and delete this file.
