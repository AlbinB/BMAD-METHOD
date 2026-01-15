# Pivot Recovery - Phase Backtracking Instructions

<critical>The workflow execution engine is governed by: {project-root}/_bmad/core/tasks/workflow.xml</critical>
<critical>You MUST have already loaded and processed: {project-root}/_custom/workflows/pivot-recovery/workflow.yaml</critical>
<critical>Communicate all responses in {communication_language} and language MUST be tailored to {user_skill_level}</critical>
<critical>Generate all documents in {document_output_language}</critical>

<critical>DOCUMENT OUTPUT: Gap analysis report, reconciliation plan, updated artifacts. Clear, actionable recovery steps.</critical>

---

## State File Management

This workflow uses TWO state files for tracking:

1. **sprint-status.yaml** - Gets `active_recovery` section to pause main workflow
2. **pivot-recovery-status.yaml** - Detailed recovery task tracking

**On Start:** Check if recovery already in progress (resume) or create new state files
**During:** Update task progress in pivot-recovery-status.yaml
**On Complete:** Remove `active_recovery` from sprint-status, mark recovery complete

---

## Workflow Purpose

This workflow helps teams recover when they discover a methodology phase was skipped or insufficiently completed before moving to implementation. Common scenarios:

- **UX Skipped**: Architecture done before UX design complete
- **Architecture Incomplete**: Implementation started before key decisions finalized
- **PRD Gaps**: Stories created without complete requirements
- **Missing Screen Specs**: UI stories created without screen requirements documents

The workflow guides you through:
1. Assessing current implementation state
2. Identifying specific gaps in the skipped phase
3. Completing the missing work
4. Reconciling downstream artifacts (architecture, epics, stories)
5. Resuming implementation with proper foundation

---

<workflow>

<step n="1" goal="Initialize Recovery Assessment">
  <action>Check for existing recovery in progress:</action>
  - Check sprint-status.yaml for `active_recovery` section
  - If exists, load pivot-recovery-status.yaml and resume from current state
  - If resuming, skip to appropriate step based on recovery progress

  <check if="active_recovery exists in sprint-status.yaml">
    <action>Load {implementation_artifacts}/pivot-recovery-status.yaml</action>
    <action>Display current recovery progress to user</action>
    <ask>"Recovery in progress. Resume from current state or start fresh?"</ask>
    <action if="resume">Skip to step matching current recovery task status</action>
  </check>

  <action>For NEW recovery - acknowledge trigger and gather context</action>
  <ask>"What phase or artifact did you discover was skipped or incomplete? Please describe what triggered this realization."</ask>

  <action>Identify the skipped phase from common scenarios:</action>
  - [ ] UX Design (screen flows, state management, error states)
  - [ ] Architecture Decisions (incomplete or assumptions made)
  - [ ] PRD/Requirements (missing acceptance criteria)
  - [ ] Screen Requirements (UI stories without detailed specs)
  - [ ] Other: ___________

  <action>Verify access to required project documents:</action>
  - Sprint status file (sprint-status.yaml)
  - Planning artifacts (PRD, Architecture, UX, Epics)
  - Implementation artifacts (story files)

  <action if="skipped phase unclear">HALT: "Need to identify which specific phase/artifact is incomplete. Please describe what work was skipped."</action>
</step>

<step n="0.5" goal="Discover and Load Project Documents">
  <invoke-protocol name="discover_inputs" />
  <note>After discovery, these content variables are available: {prd_content}, {epics_content}, {architecture_content}, {ux_design_content}, {sprint_status_content}, {story_files_content}</note>
</step>

<step n="2" goal="Assess Current Implementation State">
  <action>Analyze sprint-status.yaml to determine:</action>
  - Which epics are complete/in-progress/backlog
  - Which stories are done/in-progress/ready-for-dev/blocked
  - Current implementation momentum (how much work is "in flight")

  <action>Categorize completed work:</action>

  | Category | Impact Level | Examples |
  |----------|--------------|----------|
  | **Safe** | No rework needed | Infrastructure, backend parsers, utilities |
  | **Minor Adjustment** | Small refinements | State management, data structures |
  | **Potential Rework** | May need changes | UI components, user flows |
  | **Blocked** | Cannot proceed | Depends on missing phase work |

  <action>Present implementation state summary to user:</action>
  - Stories completed (safe)
  - Stories that may need adjustment
  - Stories blocked pending recovery

  <ask>"Does this assessment of your current state look accurate?"</ask>
</step>

<step n="3" goal="Execute Gap Analysis Checklist">
  <action>Load and execute the systematic gap analysis from: {checklist}</action>
  <action>Work through checklist interactively with user</action>

  <action>For each gap identified, record:</action>
  - Gap description (what's missing)
  - Impact assessment (what does this affect)
  - Priority (blocking vs. nice-to-have)
  - Effort estimate (small/medium/large)

  <action>Categorize gaps by type:</action>

  **Documentation Gaps** (missing from spec documents)
  - Screen flows not defined
  - State management not specified
  - Error states not designed
  - Edge cases not covered

  **Decision Gaps** (decisions not made)
  - Architecture choices assumed
  - Technology selection incomplete
  - Integration approach undefined

  **Process Gaps** (steps skipped)
  - Screen requirements documents not created
  - UX review not conducted
  - Architecture validation skipped

  <action>Present gap summary using {gap_analysis_template}</action>
</step>

<step n="3.5" goal="Create Recovery State Files">
  <action>Create pivot-recovery-status.yaml from template:</action>
  - Copy {installed_path}/templates/recovery-status-template.yaml
  - Save to {implementation_artifacts}/pivot-recovery-status.yaml
  - Populate with:
    - recovery_id: PR-{date}
    - skipped_phase from Step 1
    - gaps_identified from Step 3
    - blocked_stories from Step 2
    - recovery_tasks from gap analysis

  <action>Update sprint-status.yaml to pause main workflow:</action>
  ```yaml
  active_recovery:
    status: in-progress
    workflow: pivot-recovery
    status_file: {implementation_artifacts}/pivot-recovery-status.yaml
    triggered: {date}
    skipped_phase: "{identified_phase}"
    reason: "{gap_summary}"
    paused_at:
      epic: {current_epic}
      last_completed_story: {last_story}
    resume_at: {next_story}
  ```

  <action>Update blocked stories in sprint-status.yaml:</action>
  - Change epic status from `in-progress` to `paused-recovery`
  - Change story status from `ready-for-dev` to `blocked-recovery`

  <action>Confirm state files created</action>

  <action>Update bmm-workflow-status.yaml note field to reflect paused state:</action>
  - Locate the `dev-story` entry in workflow_status Phase 4
  - Update the `note` field to: "Paused - pivot recovery in progress ({current_epic})"
  - This provides high-level state visibility without requiring sprint-status.yaml lookup

  <note>Main workflow is now officially PAUSED</note>
</step>

<step n="4" goal="Plan Recovery Work">
  <action>Based on gap analysis, create recovery work plan:</action>

  **Phase A: Complete Skipped Work**
  - List specific tasks to complete the skipped phase
  - Identify which BMAD workflow to use for each task
  - Estimate effort for each task

  **Phase B: Reconcile Downstream Artifacts**
  - Architecture updates needed (if UX was skipped)
  - Epic/Story updates needed
  - Screen requirements to create

  **Phase C: Update Implementation Plan**
  - Stories to modify
  - New stories to add
  - Sprint status updates

  <action>Present recovery work plan to user</action>
  <ask>"Review this recovery plan. Should we adjust priorities or scope?"</ask>

  <action>Refine plan based on user feedback</action>
</step>

<step n="5" goal="Execute Recovery - Complete Skipped Phase">
  <action>Guide user through completing the skipped phase work</action>

  <check if="UX was skipped">
    <action>Route to UX Design workflow for:</action>
    - Screen-by-screen flows
    - State management specification
    - Empty/error state designs
    - User journey mapping
    <action>Reference: /bmad:bmm:workflows:create-ux-design</action>
  </check>

  <check if="Architecture gaps exist">
    <action>Route to Architecture workflow for:</action>
    - Missing architectural decisions
    - Technology selection validation
    - Integration approach definition
    <action>Reference: /bmad:bmm:workflows:create-architecture</action>
  </check>

  <check if="Screen requirements missing">
    <action>Create screen requirements documents per UX spec pattern</action>
    <action>Each screen requirement must include:</action>
    - Data sources
    - Component breakdown
    - Interactions
    - States (loading, empty, error, success)
    - Acceptance criteria
    - Edge cases
    - Dependencies
  </check>

  <action>Track completion of each recovery task</action>

  <action>After completing each task, update pivot-recovery-status.yaml:</action>
  - Set task status to `done`
  - Add output file path if applicable
  - Update progress.completed count
  - Update progress.percent_complete

  <ask>"Have you completed the skipped phase work? Ready to reconcile downstream artifacts?"</ask>
</step>

<step n="6" goal="Reconcile Downstream Artifacts">
  <action>Now that skipped work is complete, reconcile artifacts that were created based on incomplete information</action>

  **Architecture Reconciliation** (if UX changes affect architecture):
  <action>Review architecture.md against updated UX spec</action>
  <action>Identify architecture decisions that need updating:</action>
  - State management approach
  - Component structure
  - API contracts
  - Error handling patterns

  **Epic/Story Reconciliation**:
  <action>Review epics.md against updated specs</action>
  <action>Identify stories that need:</action>
  - Updated acceptance criteria
  - New screen requirement references
  - Modified scope
  - Additional stories added

  <action>For each artifact needing updates, provide specific edits:</action>
  ```
  File: [filename]
  Section: [section name]

  CURRENT:
  [current content]

  UPDATED:
  [updated content]

  Rationale: [why this change is needed]
  ```

  <ask>"Review these reconciliation changes. Approve [a], Edit [e], or Skip [s] each?"</ask>
</step>

<step n="7" goal="Update Recovery Progress">
  <action>Update pivot-recovery-status.yaml with reconciliation progress:</action>
  - Mark Phase B tasks as done
  - Add updated artifacts to artifacts_updated list
  - Update progress counts

  <action>Update artifacts_updated in pivot-recovery-status.yaml:</action>
  ```yaml
  artifacts_updated:
    - file: "planning-artifacts/ux-design-specification.md"
      changes: "Added screen flows, state management, error states"
    - file: "planning-artifacts/architecture.md"
      changes: "Updated based on UX changes (if any)"
    - file: "planning-artifacts/epics.md"
      changes: "Updated acceptance criteria (if any)"
  ```

  <action>Present current recovery progress to user</action>
  <action>Verify all Phase A and Phase B tasks are complete before proceeding</action>
</step>

<step n="8" goal="Generate Reconciliation Report">
  <action>Compile comprehensive Pivot Recovery Report using {reconciliation_template}</action>

  **Section 1: Recovery Summary**
  - What phase was skipped
  - When discovered
  - Trigger event

  **Section 2: Implementation Impact**
  - Stories completed (safe)
  - Stories requiring adjustment
  - Stories blocked (now unblocked)

  **Section 3: Recovery Work Completed**
  - Skipped phase work done
  - New artifacts created
  - Decisions made

  **Section 4: Artifact Updates**
  - Architecture changes
  - Epic/Story modifications
  - Screen requirements created

  **Section 5: Resume Plan**
  - Next story to work on
  - Any new stories added
  - Updated timeline impact (if any)

  <action>Write report to {default_output_file}</action>
  <ask>"Review the Pivot Recovery Report. Ready to finalize?"</ask>
</step>

<step n="9" goal="Finalize and Resume Implementation">
  <action>Get explicit user approval for recovery completion</action>
  <ask>"Do you approve this recovery as complete? Ready to resume implementation? (yes/no/revise)"</ask>

  <check if="no or revise">
    <action>Gather specific feedback on what needs adjustment</action>
    <goto step="5">If more skipped phase work needed</goto>
    <goto step="6">If artifact reconciliation needs refinement</goto>
  </check>

  <check if="yes">
    <action>FINALIZE pivot-recovery-status.yaml:</action>
    - Set status: complete
    - Set completed: {date}
    - Mark all tasks as done
    - Set progress.percent_complete: 100

    <action>CLEAN UP sprint-status.yaml:</action>
    - REMOVE the entire `active_recovery:` section
    - Change epic status from `paused-recovery` back to `in-progress`
    - Change story status from `blocked-recovery` back to `ready-for-dev`

    <action>Example sprint-status.yaml cleanup:</action>
    ```yaml
    # BEFORE:
    active_recovery:
      status: in-progress
      ...
    epic-3: paused-recovery
    3-6-implement-edge-connections: blocked-recovery

    # AFTER:
    # (active_recovery section REMOVED)
    epic-3: in-progress
    3-6-implement-edge-connections: ready-for-dev
    ```

    <action>Identify next story to work on</action>

    <action>Provide handoff summary:</action>
    - Recovery complete: {date}
    - Skipped phase: Now complete
    - Artifacts updated: [list]
    - Next action: Resume [story-id]

    <action>Update bmm-workflow-status.yaml note field to reflect resumed state:</action>
    - Locate the `dev-story` entry in workflow_status Phase 4
    - Update the `note` field to: "Active - {current_epic} in progress"
    - This clears the paused state and shows current epic

    <action>Route to appropriate next workflow:</action>
    - If resuming development: /bmad:bmm:workflows:dev-story
    - If creating next story: /bmad:bmm:workflows:create-story

    <note>Main workflow is now RESUMED - normal sprint operations continue</note>
  </check>
</step>

<step n="10" goal="Workflow Completion">
  <action>Summarize workflow execution:</action>
  - Skipped phase: {identified_phase}
  - Recovery duration: {start_date} to {end_date}
  - Artifacts updated: {count}
  - Stories unblocked: {count}

  <action>Confirm all deliverables produced:</action>
  - [ ] Skipped phase work completed
  - [ ] Gap analysis documented
  - [ ] Downstream artifacts reconciled
  - [ ] Sprint status updated
  - [ ] Recovery report generated

  <action>Report workflow completion: "Pivot Recovery complete, {user_name}! Implementation can now resume with proper foundation."</action>

  <action>Lessons learned prompt:</action>
  <ask>"Any insights to capture about why this phase was skipped and how to prevent it in future projects?"</ask>
</step>

</workflow>
