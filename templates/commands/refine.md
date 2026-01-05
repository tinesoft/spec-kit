---
description: Update and refine the existing feature specification and propagate changes to all related artifacts
handoffs: 
  - label: Build Technical Plan
    agent: speckit.plan
    prompt: Create a plan for the spec. I am building with...
  - label: Clarify Spec Requirements
    agent: speckit.clarify
    prompt: Clarify specification requirements
    send: true
scripts:
  sh: scripts/bash/check-prerequisites.sh --json --paths-only
  ps: scripts/powershell/check-prerequisites.ps1 -Json -PathsOnly
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Outline

The text the user typed after `/speckit.refine` describes the changes they want to make to the existing specification.

Given that refinement request, do this:

1. **Verify we're on a feature branch or setup from spec file**:
   - Run `{SCRIPT}` to get the current feature context
   - Parse JSON output for `FEATURE_DIR`, `FEATURE_SPEC`, `IMPL_PLAN`, `TASKS`, and `BRANCH_NAME`

   **If on a feature branch** (script succeeds):
   - Continue to step 2 with the feature context

   **If NOT on a feature branch** (script fails):
   - Ask the user: "No active feature branch detected. Please provide the path to the existing spec.md file you want to refine."
   - Wait for user to provide the spec.md file path in the context
   - Once provided, read the spec.md file
   - Extract the branch name from the spec file by finding the line with `**Feature Branch**:` pattern
     - Example: `**Feature Branch**: 001-module1-ai-intro-devs` → extract `001-module1-ai-intro-devs`
     - If branch name not found in spec, ERROR: "Could not find Feature Branch metadata in the provided spec.md file"
   - Fetch all remote branches: `git fetch --all --prune`
   - Check if the branch exists remotely: `git ls-remote --heads origin <branch-name>`
     - If branch exists remotely, ERROR: "Branch <branch-name> already exists remotely. Please checkout the existing branch or use a different approach."
     - If branch does NOT exist remotely, create it locally: `git checkout -b <branch-name>`
   - After creating the branch, determine the FEATURE_DIR based on branch name (e.g., `specs/<branch-name>/`)
   - Set FEATURE_SPEC to the provided spec.md path
   - Continue to step 2

   - For single quotes in args like "I'm Groot", use escape syntax: e.g 'I'\''m Groot' (or double-quote if possible: "I'm Groot")

2. **Load existing artifacts**:
   - Read the current spec file from `FEATURE_SPEC`
   - Parse all sections and understand current requirements
   - Note any existing [NEEDS CLARIFICATION] markers
   - Load existing artifacts if they exist:
     - `IMPL_PLAN` (plan.md) - technical implementation plan
     - `FEATURE_DIR/research.md` - research and technical decisions
     - `FEATURE_DIR/data-model.md` - data model
     - `FEATURE_DIR/contracts/` - API contracts
     - `TASKS` (tasks.md) - implementation tasks
     - `FEATURE_DIR/quickstart.md` - quickstart guide

3. **Apply refinements based on user input**:

   a. **Identify what to update**:
      - Parse the refinement request to determine which sections need changes
      - Common refinement types:
        - Adding new functional requirements
        - Modifying existing requirements
        - Adding/updating user stories or scenarios
        - Adding clarifications or removing ambiguities
        - Updating success criteria
        - Refining scope or assumptions
        - Adding edge cases or constraints

   b. **Make informed updates to spec.md**:
      - For additions: Add new content in the appropriate section
      - For modifications: Update existing content while preserving structure
      - For clarifications: Replace [NEEDS CLARIFICATION] markers with concrete details
      - Document changes: Add a brief note in a "Revision History" section (create if needed)

   c. **Maintain quality**:
      - Keep focus on WHAT and WHY (no implementation details)
      - Ensure all requirements remain testable
      - Update success criteria if scope changes
      - Keep the spec readable and well-structured
      - Limit new [NEEDS CLARIFICATION] markers (max 3 total across entire spec)

4. **Propagate changes to downstream artifacts**:

   a. **Update research.md** (if it exists):
      - Review if spec changes introduce new unknowns or technical decisions
      - Add research entries for new technologies or approaches needed
      - Update existing decisions if spec changes invalidate previous choices
      - Maintain the format: Decision, Rationale, Alternatives considered

   b. **Update plan.md** (if it exists):
      - Revise Technical Context to reflect new requirements
      - Update architecture or tech stack if spec changes require different approaches
      - Modify Phase 0/1 plans if new entities or contracts are needed
      - Update file structure if new components are needed
      - Ensure plan still satisfies all spec requirements

   c. **Update data-model.md** (if it exists):
      - Add new entities if spec introduces new data requirements
      - Modify existing entities if requirements change
      - Update relationships based on new user stories
      - Ensure all spec entities are represented

   d. **Update contracts/** (if directory exists):
      - Add new API endpoints for new functional requirements
      - Modify existing endpoints if requirements change
      - Remove endpoints if features are removed
      - Ensure all user actions in spec have corresponding endpoints

   e. **Update tasks.md** (if it exists):
      - Add tasks for new requirements
      - Modify tasks for changed requirements
      - Remove tasks for removed features
      - Re-organize task phases based on updated user story priorities
      - Update dependencies between tasks
      - Ensure all spec user stories have corresponding task phases

   f. **Update quickstart.md** (if it exists):
      - Revise test scenarios to match updated requirements
      - Add new test cases for new functionality
      - Update expected outcomes based on spec changes

5. **Update revision history**:
   - If "## Revision History" section doesn't exist in spec.md, create it at the end
   - Add entry: `- **[DATE]**: [Brief description of changes made]`
   - Keep history concise (1-2 sentences per revision)

6. **Validate updated specification and artifacts**:

   a. **Check quality criteria**:
      - No implementation details leaked into spec
      - All requirements are testable
      - Success criteria are measurable and technology-agnostic
      - Spec sections are complete and consistent
      - Total [NEEDS CLARIFICATION] markers ≤ 3

   b. **Check artifact consistency**:
      - All spec requirements are addressed in plan.md
      - All spec entities exist in data-model.md (if applicable)
      - All spec user actions have endpoints in contracts/ (if applicable)
      - All spec user stories have tasks in tasks.md (if applicable)
      - Research decisions align with spec needs

   c. **Update or create checklist**:
      - Load existing checklist from `FEATURE_DIR/checklists/requirements.md` if it exists
      - Update checklist items based on current spec state
      - If checklist doesn't exist, create it using the same structure as in `/speckit.specify`

   d. **Report validation results**:
      - List any quality issues found
      - List any inconsistencies between spec and artifacts
      - If critical issues exist, suggest specific fixes
      - If [NEEDS CLARIFICATION] markers remain, note them

7. **Write all updated files**:
   - Save changes to `FEATURE_SPEC` (spec.md)
   - Save changes to `IMPL_PLAN` (plan.md) if modified
   - Save changes to research.md if modified
   - Save changes to data-model.md if modified
   - Save changes to contracts/ files if modified
   - Save changes to `TASKS` (tasks.md) if modified
   - Save changes to quickstart.md if modified
   - Preserve file formatting and structure for all files
   - Ensure all markdown is properly formatted

8. **Report completion**:
   - Confirm spec has been updated
   - Show which sections were modified in spec.md
   - List all other files that were updated (research.md, plan.md, tasks.md, etc.)
   - Display revision history entry
   - Report checklist validation results
   - Report consistency check results
   - Suggest next steps:
     - If clarifications needed: Use `/speckit.clarify`
     - If ready to implement: Use `/speckit.implement`
     - If more refinements needed: Run `/speckit.refine` again

## Guidelines

### Refinement Best Practices

- **Incremental changes**: Make focused updates rather than complete rewrites
- **Preserve intent**: Keep the original feature goals unless explicitly changing them
- **Document rationale**: Explain why changes are being made in revision history
- **Propagate consistently**: Ensure all artifacts (plan, tasks, research, etc.) reflect spec changes
- **Maintain coherence**: All files should tell the same story from different perspectives
- **Validate iteratively**: Check quality and consistency after each significant change

### Common Refinement Scenarios

1. **After initial feedback**: "Add requirement for password reset functionality"
   - Update spec.md with new requirement
   - Add endpoint to contracts/ for password reset
   - Add task phase in tasks.md for implementation
   - Update plan.md if new dependencies needed

2. **After clarification**: "Update user authentication to use OAuth2 instead of basic auth"
   - Update spec.md authentication requirements
   - Update research.md with OAuth2 decision
   - Revise plan.md tech stack and architecture
   - Update contracts/ with OAuth2 endpoints
   - Revise tasks.md authentication tasks

3. **Scope refinement**: "Remove advanced reporting features, focus on basic export only"
   - Remove reporting requirements from spec.md
   - Remove reporting endpoints from contracts/
   - Remove reporting tasks from tasks.md
   - Update plan.md to remove reporting components

4. **Adding details**: "Specify that file uploads must support PDF, DOC, and DOCX formats up to 10MB"
   - Update spec.md with format/size requirements
   - Add validation to data-model.md
   - Update upload endpoints in contracts/ with constraints
   - Add validation tasks in tasks.md

5. **Fixing ambiguities**: "Clarify that 'real-time' means updates within 2 seconds"
   - Update spec.md with specific timing requirement
   - Update plan.md performance considerations
   - Add performance test in tasks.md

### What NOT to do

- Don't create a new branch or feature
- Don't change the feature numbering or directory structure
- Don't skip updating related artifacts (maintain consistency)
- Don't remove existing requirements without explicit user instruction
- Don't create duplicate or conflicting requirements across files

### Error Handling

- If spec file is missing: "Spec file not found. Run /speckit.specify first."
- If not on feature branch: "Must be on a feature branch to refine. Checkout a feature branch or create new feature with /speckit.specify."
- If refinement request is unclear: Ask clarifying questions before making changes
- If changes conflict with existing requirements: Highlight conflicts and ask for resolution
- If artifact files are missing: Note which files don't exist and will be skipped
- If changes would break consistency: Warn about inconsistencies and ask for confirmation

**NOTE:** This command modifies existing specification AND all related artifacts (research.md, plan.md, tasks.md, etc.) to maintain consistency. Always review all changes before proceeding to implementation.
