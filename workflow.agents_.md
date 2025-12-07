# AI Agent Workflow: Version Control and Change Management

## Purpose
This document defines workflow requirements for AI agents working with version control systems and change tracking. These rules ensure accountability, traceability, and proper human oversight integration.

---

## 1. **Commit Message Standards**

### Required Format
All commits made by AI agents **must** follow this format:
```
[AI:<agent-type>] <description> — <ISO-timestamp>
```

**Agent Types:**
- `HITL` - Human-in-the-Loop agent (requires human review before commit)
- `TASK` - Autonomous Task agent (pre-approved workflow)
- `SUGGEST` - Suggestion-only agent (no direct commits)

**Examples:**
```
[AI:HITL] Refactor authentication module — 2025-12-07T09:00:00Z
[AI:TASK] Update dependencies per security scan — 2025-12-07T14:30:00Z
[AI:SUGGEST] Propose API endpoint optimization — 2025-12-07T16:45:00Z
```

### Commit Message Content
- **Concise but descriptive**: Clearly state what was changed and why
- **Scope indication**: Mention affected components/modules when relevant
- **Reference issues**: Include issue/ticket numbers when applicable (e.g., `fixes #123`)
- **Multi-line format** for complex changes:
  ```
  [AI:HITL] Refactor authentication module — 2025-12-07T09:00:00Z
  
  - Split monolithic auth.py into modular components
  - Add JWT token validation with expiry checks
  - Update tests for new auth flow
  
  Refs: #456
  ```

---

## 2. **Version Control Best Practices**

### Mandatory Rules
- **Never modify git config**: Do not change author/committer information. Commits should reflect the authenticated user who authorized the agent.
- **Preserve history**: Always use `git mv` for file moves/renames to maintain Git history tracking.
- **Atomic commits**: Each commit should represent a single logical change. Break large changes into multiple commits when appropriate.
- **Branch strategy**: 
  - HITL agents should work on feature branches and create pull requests
  - TASK agents may commit to approved branches per workflow configuration
  - Never commit directly to protected branches without explicit authorization

### Recommended Practices
- Run `git status` before committing to verify intended changes
- Use `git diff` to review changes before staging
- Stage related changes together: `git add <specific-files>` rather than `git add .`
- Verify tests pass before committing when applicable

---

## 3. **Change Tracking and Documentation**

### Change Log Entries
AI agents must maintain entries in the designated change log file (e.g., `ref/TEMP_INSTR_CHANGES.md` or similar) for all modifications:

**Required Information:**
```markdown
### [AI:<agent-type>] <Description> — <ISO-timestamp>
**Status:** [Pending Review | Approved | Rejected]
**Agent:** <agent-identifier>
**Files Modified:**
- path/to/file1.ext
- path/to/file2.ext

**Changes Summary:**
<Brief description of what was changed and why>

**Review Notes:**
<Space for human reviewer comments>

---
```

### Change Log Lifecycle
- **Agent Responsibility**: Create entries when changes are made
- **Human Responsibility**: Review and update status; remove entries once changes are integrated or rejected
- **Persistence Rule**: Agents **must not** delete or modify pending entries automatically. Only human reviewers may remove entries.
- **Audit Trail**: Even after removal from active change log, maintain archived records for compliance/audit purposes

### Change Log Location
- Default: `ref/TEMP_INSTR_CHANGES.md` (or as configured in repository)
- For large projects: Consider per-module change logs
- Ensure change log path is documented in repository README

---

## 4. **Integration with AI Attribution Policy**

All workflow practices must align with the AI Attribution and Responsibility guidelines:
- Code changes must include appropriate AI-generated disclaimers
- Respect annotated code restrictions during commits
- Human-in-the-loop agents must await approval before pushing commits
- Autonomous task agents must operate within pre-approved workflows only

---

## 5. **Error Handling and Rollback**

### Commit Failures
If a commit fails:
1. Log the error with full context
2. Notify the human operator (for HITL agents)
3. Do not retry automatically without human intervention
4. Preserve working directory state for manual review

### Rollback Procedures
- Document rollback steps in change log entries
- For TASK agents: Implement automated rollback on workflow failure
- Maintain clear audit trail of rollback actions

---

*These workflow rules are mandatory for all AI agent operations in this repository. For questions, contact the repository maintainers.*
