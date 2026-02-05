# AI Agent: Cross-Repository Orchestration

## Purpose
This document describes how to use the `copy-feed-files` tool to orchestrate agentic tasks across multiple repositories in an organization. This pattern enables distributed AI workflows where agents can communicate and coordinate work through feed files.

---

## Overview

The `copy-feed-files` tool enables cross-repository agent communication by:
1. Searching all organization repositories for feed-out files destined for the current repo
2. Copying them as feed-in files that local agents can process
3. Allowing agents to read task requests, data, or instructions from other repositories

**Pattern:** Repositories publish `.{target-repo}.feed-out.md` files that get copied to `{target-repo}` as `.feed-in.md` files.

---

## Basic Usage

### Running the Tool

```bash
# From the target repository (e.g., 'scripts' repo)
cd /path/to/scripts
go run copy-feed-files.go

# Or build and run
go build copy-feed-files.go
./copy-feed-files
```

The tool will:
- Detect the current repo name (e.g., "scripts")
- Search the organization for files like `analysis.scripts.feed-out.md`
- Copy them to the current repo as `analysis.feed-in.md` at the same path

---

## Orchestration Patterns

### Pattern 1: Task Distribution

**Use Case:** A central orchestrator repository distributes tasks to worker repositories.

**Setup:**
1. **Orchestrator repo** (`orchestrator`) creates task files:
   ```
   tasks/task-001.scripts.feed-out.md
   tasks/task-002.api.feed-out.md
   tasks/task-003.frontend.feed-out.md
   ```

2. **Worker repos** run `copy-feed-files` to fetch their tasks:
   ```bash
   # In scripts repo
   ./copy-feed-files
   # Creates: tasks/task-001.feed-in.md
   
   # In api repo
   ./copy-feed-files
   # Creates: tasks/task-002.feed-in.md
   ```

3. **Worker agents** process their feed-in files and complete tasks

**Task File Format:**
```markdown
# Task: Generate Test Coverage Report

**Agent Type:** TASK
**Priority:** High
**Deadline:** 2026-02-06T12:00:00Z

## Objective
Generate a comprehensive test coverage report for all modules.

## Steps
1. Run test suite with coverage enabled
2. Generate HTML coverage report
3. Analyze coverage gaps
4. Create summary in `reports/coverage-2026-02-05.md`

## Output Required
- Coverage report file
- Summary of untested modules
- Recommendations for improvement

## Completion Signal
Create `tasks/task-001.completed.orchestrator.feed-out.md` with results.
```

### Pattern 2: Data Aggregation

**Use Case:** Multiple repositories send data to a central analysis repository.

**Setup:**
1. **Source repos** create data feed-out files:
   ```
   # In api repo
   metrics/daily-stats.analytics.feed-out.md
   
   # In frontend repo
   metrics/user-interactions.analytics.feed-out.md
   
   # In backend repo
   metrics/performance.analytics.feed-out.md
   ```

2. **Analytics repo** runs `copy-feed-files`:
   ```bash
   # In analytics repo
   ./copy-feed-files
   # Creates:
   #   metrics/daily-stats.feed-in.md
   #   metrics/user-interactions.feed-in.md
   #   metrics/performance.feed-in.md
   ```

3. **Analytics agent** processes all feed-in files and generates combined insights

### Pattern 3: Dependency Coordination

**Use Case:** A repository needs to wait for another repository to complete work before proceeding.

**Setup:**
1. **Backend repo** signals readiness:
   ```
   status/api-v2-ready.frontend.feed-out.md
   ```

2. **Frontend repo** polls with `copy-feed-files`:
   ```bash
   # Run periodically or on-demand
   ./copy-feed-files
   ```

3. **Frontend agent** checks for `status/api-v2-ready.feed-in.md` before starting API migration

**Status File Format:**
```markdown
# Status: API v2 Migration Complete

**Status:** Ready
**Timestamp:** 2026-02-05T20:15:00Z
**Version:** 2.0.0

## Changes
- New authentication endpoints
- Updated user management API
- Breaking changes documented in MIGRATION.md

## Frontend Actions Required
1. Update API client to use v2 endpoints
2. Handle new error response format
3. Test authentication flow

## Support
Contact: backend-team@example.com
```

### Pattern 4: Bidirectional Communication

**Use Case:** Two repositories need to exchange information and coordinate work.

**Setup:**
1. **Repo A** sends request:
   ```
   requests/schema-validation.repo-b.feed-out.md
   ```

2. **Repo B** runs `copy-feed-files`, gets `requests/schema-validation.feed-in.md`

3. **Repo B** processes request and sends response:
   ```
   responses/schema-validation-result.repo-a.feed-out.md
   ```

4. **Repo A** runs `copy-feed-files`, gets response and continues work

---

## Agent Workflow Integration

### Automated Orchestration Workflow

```markdown
### [AI:TASK] Cross-Repo Task Execution — 2026-02-05T22:00:00Z
**Workflow ID:** cross-repo-001
**Total Steps:** 7

**Step Checklist:**
- [x] Step 1: Run copy-feed-files to fetch tasks
  - Files: tasks/*.feed-in.md
  - Notes: Found 3 new task files
- [x] Step 2: Parse task files and validate format
  - Notes: All tasks valid, prioritized by deadline
- [ ] Step 3: Execute highest priority task (task-001)
  - Status: In Progress
  - Notes: Generating test coverage report
- [ ] Step 4: Create completion signal file
  - Files: tasks/task-001.completed.orchestrator.feed-out.md
  - Notes: Pending task completion
- [ ] Step 5: Commit completion signal
  - Notes: Will use [AI:TASK] commit message
- [ ] Step 6: Push to remote
  - Notes: Trigger orchestrator to fetch results
- [ ] Step 7: Clean up feed-in files
  - Notes: Archive processed tasks

**Validation Checkpoints:**
- [ ] All task requirements completed
- [ ] Completion signal file contains all requested output
- [ ] No errors during execution
```

### Integration with CI/CD

Add `copy-feed-files` to your CI pipeline:

```yaml
# .github/workflows/check-feed-files.yml
name: Check Feed Files

on:
  schedule:
    - cron: '*/15 * * * *'  # Every 15 minutes
  workflow_dispatch:

jobs:
  check-feeds:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-go@v4
        with:
          go-version: '1.21'
      
      - name: Run copy-feed-files
        run: |
          cd scripts
          go run copy-feed-files.go
      
      - name: Check for new feed files
        id: check
        run: |
          if [ -n "$(git status --porcelain)" ]; then
            echo "new_feeds=true" >> $GITHUB_OUTPUT
          fi
      
      - name: Trigger agent processing
        if: steps.check.outputs.new_feeds == 'true'
        run: |
          # Trigger your agent workflow
          gh workflow run process-feeds.yml
```

---

## Best Practices

### 1. File Naming Convention
- **Pattern:** `{descriptive-name}.{target-repo}.feed-out.md`
- Use kebab-case for descriptive names
- Be specific about content (e.g., `user-metrics`, `deploy-status`, `test-results`)

### 2. File Organization
```
repo-structure/
├── tasks/           # Task assignments
│   └── *.feed-in.md
├── status/          # Status updates
│   └── *.feed-in.md
├── requests/        # Information requests
│   └── *.feed-in.md
├── responses/       # Request responses
│   └── *.feed-in.md
└── data/            # Data transfers
    └── *.feed-in.md
```

### 3. Feed File Lifecycle
- **Create:** Source repo creates `.{target}.feed-out.md`
- **Transfer:** Target repo runs `copy-feed-files`
- **Process:** Target agent reads `.feed-in.md` and acts
- **Complete:** Target agent creates response or completion signal
- **Archive:** Both repos archive processed feed files

### 4. Error Handling
```markdown
# Feed File Error Report

**Feed File:** tasks/task-001.feed-in.md
**Error:** Unable to complete task
**Timestamp:** 2026-02-05T22:30:00Z

## Error Details
Invalid parameters in task specification: missing required field 'output_format'

## Resolution
- Create error report: `errors/task-001-error.orchestrator.feed-out.md`
- Request clarification from source repo
- Mark task as failed in workflow log
```

### 5. Security Considerations
- **Validate all feed-in files** before processing
- **Never execute code** directly from feed files
- **Limit file sizes** to prevent resource exhaustion
- **Sanitize paths** to prevent directory traversal
- **Audit feed file sources** and content
- **Use repository permissions** to control who can create feed-out files

### 6. Monitoring and Observability
- Log all `copy-feed-files` executions
- Track feed file processing times
- Alert on failed file transfers
- Monitor feed file queue depths
- Report on orchestration workflow health

---

## Example: Complete Orchestration Scenario

**Scenario:** Deploy a new feature requiring coordinated changes across 3 repos.

### Step 1: Orchestrator Creates Deployment Plan
```bash
# In orchestrator repo
cat > deploy/feature-x.backend.feed-out.md << 'EOF'
# Deploy Task: Feature X Backend Changes
**Task ID:** deploy-feature-x-001
**Phase:** 1 of 3
**Deadline:** 2026-02-06T10:00:00Z

## Backend Changes Required
1. Add new API endpoints in `src/api/feature-x/`
2. Run migrations for feature_x tables
3. Deploy to staging
4. Run integration tests
5. Signal completion to orchestrator

## Completion Signal
Create: `deploy/feature-x-backend-complete.orchestrator.feed-out.md`
EOF

cat > deploy/feature-x.frontend.feed-out.md << 'EOF'
# Deploy Task: Feature X Frontend Changes
**Task ID:** deploy-feature-x-002
**Phase:** 2 of 3 (wait for backend completion)
**Deadline:** 2026-02-06T14:00:00Z

## Prerequisites
Wait for: `deploy/feature-x-backend-complete.feed-in.md`

## Frontend Changes Required
1. Add Feature X UI components
2. Integrate with new backend API
3. Update routing
4. Deploy to staging
5. Run E2E tests
6. Signal completion

## Completion Signal
Create: `deploy/feature-x-frontend-complete.orchestrator.feed-out.md`
EOF

git add deploy/
git commit -m "[AI:TASK] Create Feature X deployment plan — 2026-02-05T22:00:00Z"
git push
```

### Step 2: Backend Agent Executes Task
```bash
# In backend repo (triggered by CI or manual)
./copy-feed-files
# Receives: deploy/feature-x.feed-in.md

# Agent processes task (details in workflow.agents_.md)
# ... makes changes, runs tests ...

# Create completion signal
cat > deploy/feature-x-backend-complete.orchestrator.feed-out.md << 'EOF'
# Feature X Backend: Complete
**Status:** Success
**Completed:** 2026-02-05T23:30:00Z

## Changes Deployed
- API endpoints: /api/v1/feature-x/*
- Database migrations: v2026020501
- Staging URL: https://api-staging.example.com

## Test Results
- Unit tests: ✓ 45/45 passed
- Integration tests: ✓ 12/12 passed

## Next Steps
Frontend can now proceed with Phase 2.
EOF

git add deploy/
git commit -m "[AI:TASK] Complete Feature X backend deployment — 2026-02-05T23:30:00Z"
git push
```

### Step 3: Orchestrator Monitors Progress
```bash
# In orchestrator repo
./copy-feed-files
# Receives: deploy/feature-x-backend-complete.feed-in.md

# Verify backend completion, no manual intervention needed
```

### Step 4: Frontend Agent Executes Task
```bash
# In frontend repo
./copy-feed-files
# Receives: 
#   - deploy/feature-x.feed-in.md (original task)
#   - deploy/feature-x-backend-complete.feed-in.md (prerequisite)

# Agent waits for prerequisite, then processes task
# ... makes changes, runs tests ...

# Create completion signal
cat > deploy/feature-x-frontend-complete.orchestrator.feed-out.md << 'EOF'
# Feature X Frontend: Complete
**Status:** Success
**Completed:** 2026-02-06T02:00:00Z

## Changes Deployed
- New components in src/features/feature-x/
- Staging URL: https://app-staging.example.com

## Test Results
- Unit tests: ✓ 28/28 passed
- E2E tests: ✓ 8/8 passed

## Ready for Production
All staging tests passed. Ready for prod deployment approval.
EOF

git add deploy/
git commit -m "[AI:TASK] Complete Feature X frontend deployment — 2026-02-06T02:00:00Z"
git push
```

### Step 5: Orchestrator Completes Workflow
```bash
# In orchestrator repo
./copy-feed-files
# Receives: deploy/feature-x-frontend-complete.feed-in.md

# Orchestrator verifies all phases complete
# Creates deployment summary
# Requests human approval for production deployment
```

---

## Troubleshooting

### Feed files not appearing
- Verify the source repo has pushed `.{target-repo}.feed-out.md` files
- Check that `copy-feed-files` is targeting the correct organization
- Ensure GitHub CLI (`gh`) is authenticated
- Verify file naming matches pattern exactly

### Permission errors
- Check GitHub token has `repo` scope
- Verify organization membership
- Ensure target directory is writable

### Performance issues
- Limit feed file sizes (< 1MB recommended)
- Use specific paths rather than root-level searches
- Consider batching feed files
- Implement feed file expiration/cleanup

---

*This orchestration pattern enables scalable multi-repository AI agent workflows. For questions or enhancements, contact the platform team.*
