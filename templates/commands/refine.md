---
description: Update and refine the existing feature specification in the current branch
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

1. **Verify we're on a feature branch**:
   - Run `{SCRIPT}` to get the current feature context
   - Parse JSON output for `FEATURE_DIR`, `FEATURE_SPEC`, and `BRANCH_NAME`
   - If not on a feature branch or spec doesn't exist, ERROR: "No active feature found. Use /speckit.specify to create a new feature first."
   - For single quotes in args like "I'm Groot", use escape syntax: e.g 'I'\''m Groot' (or double-quote if possible: "I'm Groot")

2. **Load existing specification**:
   - Read the current spec file from `FEATURE_SPEC`
   - Parse all sections and understand current requirements
   - Note any existing [NEEDS CLARIFICATION] markers

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

   b. **Make informed updates**:
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

4. **Update revision history**:
   - If "## Revision History" section doesn't exist, create it at the end of the spec
   - Add entry: `- **[DATE]**: [Brief description of changes made]`
   - Keep history concise (1-2 sentences per revision)

5. **Validate updated specification**:

   a. **Check quality criteria**:
      - No implementation details leaked in
      - All requirements are testable
      - Success criteria are measurable and technology-agnostic
      - Spec sections are complete and consistent
      - Total [NEEDS CLARIFICATION] markers ≤ 3

   b. **Update or create checklist**:
      - Load existing checklist from `FEATURE_DIR/checklists/requirements.md` if it exists
      - Update checklist items based on current spec state
      - If checklist doesn't exist, create it using the same structure as in `/speckit.specify`

   c. **Report validation results**:
      - List any quality issues found
      - If critical issues exist, suggest specific fixes
      - If [NEEDS CLARIFICATION] markers remain, note them

6. **Write updated specification**:
   - Save changes to `FEATURE_SPEC`
   - Preserve file formatting and structure
   - Ensure all markdown is properly formatted

7. **Report completion**:
   - Confirm spec has been updated
   - Show which sections were modified
   - Display revision history entry
   - Report checklist validation results
   - Suggest next steps:
     - If clarifications needed: Use `/speckit.clarify`
     - If spec is ready: Use `/speckit.plan` or `/speckit.implement`
     - If more refinements needed: Run `/speckit.refine` again

## Guidelines

### Refinement Best Practices

- **Incremental changes**: Make focused updates rather than complete rewrites
- **Preserve intent**: Keep the original feature goals unless explicitly changing them
- **Document rationale**: Explain why changes are being made in revision history
- **Stay spec-focused**: Don't drift into planning or implementation details
- **Validate iteratively**: Check quality after each significant change

### Common Refinement Scenarios

1. **After initial feedback**: "Add requirement for password reset functionality"
2. **After clarification**: "Update user authentication to use OAuth2 instead of basic auth"
3. **Scope refinement**: "Remove advanced reporting features, focus on basic export only"
4. **Adding details**: "Specify that file uploads must support PDF, DOC, and DOCX formats up to 10MB"
5. **Fixing ambiguities**: "Clarify that 'real-time' means updates within 2 seconds"

### What NOT to do

- Don't create a new branch or feature
- Don't change the feature numbering or directory structure
- Don't add implementation details (frameworks, APIs, code structure)
- Don't remove existing requirements without explicit user instruction
- Don't create duplicate or conflicting requirements

### Error Handling

- If spec file is missing: "Spec file not found. Run /speckit.specify first."
- If not on feature branch: "Must be on a feature branch to refine. Checkout a feature branch or create new feature with /speckit.specify."
- If refinement request is unclear: Ask clarifying questions before making changes
- If changes conflict with existing requirements: Highlight conflicts and ask for resolution

**NOTE:** This command modifies the existing spec file in place. Always review changes before proceeding to planning or implementation.
