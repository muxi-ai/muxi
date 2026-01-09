# Schema Fixes Needed - 2026-01-09

**Critical Issue:** Documentation was written with INCORRECT schema structures. All examples and references must be fixed against the actual schema in `/Users/ran/Projects/muxi/code/vscode/schemas/formation.schema.json`

**Official Schema Reference:** https://github.com/agent-formation/afs-spec

---

## Issues Found

### 1. Persona - COMPLETELY WRONG ❌

**What I wrote (MADE UP):**
```yaml
overlord:
  persona:
    name: "Assistant"
    style: "professional"
    tone: "helpful"
    traits: [knowledgeable, efficient]
```

**Actual Schema:**
```yaml
overlord:
  persona: "You are a helpful assistant..."  # Just a STRING!
```

**Impact:** 
- concepts/persona.md - Wrong examples throughout
- reference/persona.md - Entire page is wrong
- All 5 example formation.yaml files

---

### 2. Workflow - WRONG PATH ❌

**What I wrote:**
```yaml
workflow:  # Wrong - should be overlord.workflow
  max_parallel_tasks: 10
```

**Actual Schema:**
```yaml
overlord:
  workflow:  # Under overlord!
    max_parallel_tasks: 5  # Default is 5, not 10
```

**Impact:**
- concepts/workflows.md - Wrong path in all examples
- reference/workflows.md - Wrong path throughout
- All 5 example formation.yaml files

---

### 3. Approvals - WRONG FIELD NAMES ❌

**What I wrote:**
```yaml
workflow:
  requires_approval: true      # Doesn't exist
  approval_threshold: 10       # Wrong name
```

**Actual Schema:**
```yaml
overlord:
  workflow:
    plan_approval_threshold: 7  # Correct name and path
```

**Impact:**
- concepts/approvals.md - Wrong field names
- reference/approvals.md - Wrong field names
- All 5 example formation.yaml files

---

### 4. Async - WRONG PATH ❌

**What I wrote:**
```yaml
workflow:
  async_threshold: 10  # Wrong path
```

**Actual Schema:**
```yaml
async:  # Top-level field, not under workflow!
  threshold_seconds: 30  # Different field name
```

**Impact:**
- concepts/async.md - Wrong path
- All 5 example formation.yaml files

---

## Files Requiring Fixes (13 files)

### Concept Docs (4 files)
1. ✅ `docs/concepts/persona.md` - Remove style/tone/traits, show it's just a string
2. ✅ `docs/concepts/workflows.md` - Fix paths to overlord.workflow
3. ✅ `docs/concepts/approvals.md` - Fix field names to plan_approval_threshold
4. ✅ `docs/concepts/async.md` - Fix path to top-level async

### Reference Docs (3 files)
5. ✅ `docs/reference/persona.md` - Rewrite entirely (it's just a string!)
6. ✅ `docs/reference/workflows.md` - Fix all paths and default values
7. ✅ `docs/reference/approvals.md` - Fix field names

### Examples (5 files)
8. ✅ `docs/examples/01-simple-chatbot/formation.yaml`
9. ✅ `docs/examples/02-customer-support/formation.yaml`
10. ✅ `docs/examples/03-research-assistant/formation.yaml`
11. ✅ `docs/examples/04-code-reviewer/formation.yaml`
12. ✅ `docs/examples/05-multi-agent-team/formation.yaml`

### New File Needed (1 file)
13. ✅ `docs/concepts/formation-schema.md` - Comprehensive schema reference (link to https://github.com/agent-formation/afs-spec)

---

## Fix Strategy

1. **Read full schema** - `/Users/ran/Projects/muxi/code/vscode/schemas/formation.schema.json`
2. **Fix concept docs** - Update examples with correct schema
3. **Fix reference docs** - Rewrite with correct field names and paths
4. **Fix example YAMLs** - Validate against actual schema
5. **Create schema doc** - Comprehensive formation-schema.md reference
6. **Update SUMMARY.md** - Add Formation Schema link
7. **Commit fixes** - Single commit with all corrections

---

## Notes

- Schema file: `/Users/ran/Projects/muxi/code/vscode/schemas/formation.schema.json` (1,166 lines)
- Official spec: https://github.com/agent-formation/afs-spec
- All fixes must validate against the actual schema
- No more "made up" structures!

---

**Status:** FIXING IN PROGRESS
**Priority:** CRITICAL (blocks v1.0 launch)
