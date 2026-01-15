# Pivot Recovery - Workflow Orchestration Pattern

**Created:** 2026-01-14
**Purpose:** Document how pivot-recovery integrates with the main sprint workflow

---

## Overview

The pivot-recovery workflow implements an "interrupt and resume" pattern that pauses the main sprint workflow while recovery work is completed, then cleanly merges back.

---

## State Files

### 1. sprint-status.yaml (Main Workflow State)

When recovery is active, contains:

```yaml
active_recovery:
  status: in-progress
  workflow: pivot-recovery
  status_file: _bmad-output/implementation-artifacts/pivot-recovery-status.yaml
  triggered: 2026-01-14
  skipped_phase: "UX Design"
  reason: "UX design incomplete before architecture"
  paused_at:
    epic: epic-3
    last_completed_story: 3-5-create-step-node-component
  resume_at: 3-6-implement-edge-connections
```

Story/Epic statuses during recovery:
- Epic: `paused-recovery` (was `in-progress`)
- Stories: `blocked-recovery` (was `ready-for-dev`)

### 2. pivot-recovery-status.yaml (Recovery Task Tracking)

Detailed tracking of recovery work:

```yaml
recovery_id: PR-2026-01-14
status: in-progress  # not-started, in-progress, complete
triggered: 2026-01-14
completed: null

trigger:
  skipped_phase: "UX Design"
  reason: "..."
  gaps_identified: [...]

main_workflow_state:
  paused_at: {...}
  blocked_stories: {...}
  total_blocked: 12

recovery_tasks:
  - id: PR-1
    phase: "A - Complete Skipped Work"
    task: "Complete screen-by-screen flows"
    status: pending  # pending, in-progress, done, skipped
    workflow: create-ux-design
    output: null

artifacts_updated: []

resume_plan:
  next_story: 3-6-implement-edge-connections
  next_workflow: dev-story

progress:
  total_tasks: 6
  completed: 0
  percent_complete: 0
```

---

## Flow Diagram

```
Normal Sprint Flow:
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│ /sprint-status  │───►│ /create-story   │───►│ /dev-story      │
└─────────────────┘    └─────────────────┘    └─────────────────┘

Recovery Interrupt:
┌─────────────────┐    ┌─────────────────────────────────────────┐
│ /sprint-status  │───►│ ⚠️ RECOVERY MODE DETECTED               │
└─────────────────┘    │   Sprint PAUSED - Run /pivot-recovery   │
                       └─────────────────────────────────────────┘
                                          │
                                          ▼
                       ┌─────────────────────────────────────────┐
                       │ /pivot-recovery                         │
                       │   • Complete skipped phase work         │
                       │   • Reconcile artifacts                 │
                       │   • Update recovery-status.yaml         │
                       └─────────────────────────────────────────┘
                                          │
                                          ▼ (on complete)
                       ┌─────────────────────────────────────────┐
                       │ CLEANUP:                                │
                       │   • Remove active_recovery from sprint  │
                       │   • blocked-recovery → ready-for-dev    │
                       │   • Resume normal flow                  │
                       └─────────────────────────────────────────┘
```

---

## Workflow Integration Points

### /sprint-status Behavior

When `active_recovery` exists in sprint-status.yaml:
1. Displays recovery warning banner
2. Shows recovery progress from pivot-recovery-status.yaml
3. Lists blocked stories
4. Routes to /pivot-recovery instead of normal sprint options
5. Does NOT show normal sprint status (recovery takes precedence)

### /pivot-recovery Behavior

**On Start (Step 1):**
- Checks for existing `active_recovery` in sprint-status.yaml
- If exists: loads pivot-recovery-status.yaml and resumes
- If new: proceeds with gap analysis

**After Gap Analysis (Step 3.5):**
- Creates pivot-recovery-status.yaml from template
- Updates sprint-status.yaml with `active_recovery` section
- Changes story statuses to `blocked-recovery`
- Changes epic statuses to `paused-recovery`

**During Recovery (Steps 5-7):**
- Updates task status in pivot-recovery-status.yaml
- Tracks artifacts_updated list
- Updates progress counts

**On Completion (Step 9):**
- Sets pivot-recovery-status.yaml status to `complete`
- REMOVES `active_recovery` section from sprint-status.yaml
- Changes `blocked-recovery` back to `ready-for-dev`
- Changes `paused-recovery` back to `in-progress`
- Routes to /dev-story or /create-story

---

## Status Values

### Story Statuses
- `backlog` - Story only in epic file
- `ready-for-dev` - Story file created
- `in-progress` - Developer working
- `review` - Ready for code review
- `done` - Completed
- `blocked-recovery` - **NEW** Blocked pending pivot recovery

### Epic Statuses
- `backlog` - Not started
- `in-progress` - Actively being worked
- `done` - All stories complete
- `paused-recovery` - **NEW** Paused for pivot recovery

---

## Recovery Task Phases

### Phase A: Complete Skipped Work
Tasks to complete the skipped methodology phase (e.g., UX Design).
Routes to appropriate BMAD workflows.

### Phase B: Reconcile Artifacts
Review and update downstream artifacts that were created based on incomplete information:
- Architecture decisions
- Epic/story acceptance criteria
- Screen requirements

### Phase C: Validate and Resume
- Update sprint-status.yaml
- Unblock stories
- Resume normal sprint operations

---

## Files in This Workflow

```
_custom/workflows/pivot-recovery/
├── workflow.yaml                           # Main config
├── instructions.md                         # 10-step workflow with state management
├── checklist.md                            # Gap analysis checklist
├── context/
│   └── workflow-orchestration.md           # This file
└── templates/
    ├── gap-analysis-template.md            # Gap documentation
    ├── reconciliation-report-template.md   # Final report
    └── recovery-status-template.yaml       # Recovery tracking template
```

---

## Invocation

**Claude Code:**
```
/pivot-recovery
```

**AntiGravity:**
```
bmad-custom-workflows-pivot-recovery
```

**Direct Load:**
```
Load and run _custom/workflows/pivot-recovery/workflow.yaml
```

---

## Design Decisions

1. **Separate status file** - Recovery tracking in its own file keeps sprint-status.yaml clean and makes recovery a first-class artifact

2. **Pointer in sprint-status** - `active_recovery` section provides quick detection without parsing separate file

3. **Blocked statuses** - New `blocked-recovery` and `paused-recovery` statuses make state explicit and prevent accidental work on blocked stories

4. **Resume capability** - Workflow can be interrupted and resumed by checking existing state files

5. **Clean merge** - On completion, all recovery state is removed and normal workflow resumes seamlessly
