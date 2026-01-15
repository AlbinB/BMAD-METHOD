# Pivot Recovery - Gap Analysis Checklist

Use this checklist to systematically identify gaps in the skipped phase and assess impact on downstream artifacts.

---

## Section 1: Identify Skipped Phase

**Which methodology phase was skipped or incomplete?**

- [ ] **Phase 1 - Analysis** (Brainstorming, Research, Product Brief)
- [ ] **Phase 2 - Planning** (PRD, UX Design)
- [ ] **Phase 3 - Solutioning** (Architecture, Epics & Stories, Test Design)
- [ ] **Phase 4 - Implementation** (Sprint Planning incomplete)

**What triggered the discovery?**

- [ ] Implementation blocked - missing information
- [ ] Code review revealed assumptions
- [ ] User feedback during demo
- [ ] Self-discovery during development
- [ ] Other: ___________

---

## Section 2: UX Design Gap Analysis

*Complete if UX Design was skipped or incomplete*

### Screen-by-Screen Flows

- [ ] All user journeys mapped
- [ ] Entry points defined
- [ ] Exit points defined
- [ ] Navigation between screens documented
- [ ] Deep linking requirements specified

**Gaps identified:**
- ___________

### State Management UX

- [ ] Selection persistence rules defined (what persists across views)
- [ ] View-specific state documented (zoom, scroll, filters)
- [ ] Global state identified (user selection, preferences)
- [ ] State transitions specified (what triggers state changes)
- [ ] Browser back/forward behavior defined

**Gaps identified:**
- ___________

### Empty States

- [ ] Initial load state designed
- [ ] No data state designed
- [ ] No results (after search/filter) designed
- [ ] First-time user experience designed

**Gaps identified:**
- ___________

### Error States

- [ ] Network error states designed
- [ ] Validation error states designed
- [ ] Permission error states designed
- [ ] Recovery paths defined (how user gets back to good state)
- [ ] Error message copy written

**Gaps identified:**
- ___________

### Loading States

- [ ] Initial load indicators designed
- [ ] In-progress operation indicators designed
- [ ] Skeleton screens defined
- [ ] Progress feedback for long operations

**Gaps identified:**
- ___________

### Screen Requirements Documents

- [ ] Template exists for screen requirements
- [ ] Data sources documented per screen
- [ ] Component breakdown per screen
- [ ] Interactions per screen
- [ ] Acceptance criteria per screen
- [ ] Edge cases per screen

**Gaps identified:**
- ___________

---

## Section 3: Architecture Gap Analysis

*Complete if Architecture decisions may be affected*

### Decisions Potentially Based on Assumptions

- [ ] State management approach (chosen without UX input?)
- [ ] API contract design (missing UX requirements?)
- [ ] Component structure (assumed without screen specs?)
- [ ] Error handling patterns (designed without error state specs?)
- [ ] Caching strategy (performance requirements unclear?)

**Decisions to revisit:**
- ___________

### Cross-Cutting Concerns

- [ ] Loading state patterns align with UX spec
- [ ] Error boundary strategy supports UX error states
- [ ] Navigation architecture supports UX flows
- [ ] State persistence matches UX requirements

**Misalignments identified:**
- ___________

---

## Section 4: Epic & Story Impact

*Assess which stories are affected*

### Stories Dependent on Missing Information

| Story ID | Title | Missing Input | Impact Level |
|----------|-------|---------------|--------------|
| | | | Safe / Minor / Major / Blocked |
| | | | |
| | | | |

### Stories That May Need Updates

- [ ] Acceptance criteria incomplete
- [ ] Screen requirements reference missing
- [ ] Edge cases not covered
- [ ] Error handling not specified

**Stories to update:**
- ___________

### New Stories Potentially Needed

- [ ] Screen requirement creation stories
- [ ] Error state implementation stories
- [ ] Empty state implementation stories
- [ ] State management refinement stories

**New stories to add:**
- ___________

---

## Section 5: Implementation State Assessment

### Work Completed (Safe)

*List stories/work that doesn't depend on missing phase*

| Story | Why Safe |
|-------|----------|
| | |

### Work Requiring Adjustment

*List stories that may need minor updates*

| Story | What Needs Adjustment |
|-------|----------------------|
| | |

### Work Blocked

*List stories that cannot proceed without recovery*

| Story | Blocking Gap |
|-------|--------------|
| | |

---

## Section 6: Recovery Priority

### Blocking Gaps (Must Fix)

*Gaps that block continued implementation*

1. ___________
2. ___________
3. ___________

### Important Gaps (Should Fix)

*Gaps that will cause problems if not addressed*

1. ___________
2. ___________
3. ___________

### Nice-to-Have Gaps (Could Fix)

*Gaps that could be addressed later or accepted as-is*

1. ___________
2. ___________
3. ___________

---

## Section 7: Recovery Approach

**Recommended approach based on assessment:**

- [ ] **Minimal**: Fix only blocking gaps, accept minor technical debt
- [ ] **Standard**: Fix blocking + important gaps, defer nice-to-have
- [ ] **Comprehensive**: Fix all identified gaps before resuming

**Rationale:**
___________

**Estimated recovery effort:**

- [ ] Small (1-2 days)
- [ ] Medium (3-5 days)
- [ ] Large (1-2 weeks)
- [ ] Significant (requires replanning)

---

## Checklist Complete

- [ ] All relevant sections completed
- [ ] Gaps prioritized
- [ ] Recovery approach selected
- [ ] Impact on stories assessed

**Ready to proceed to recovery planning: Yes / No**
