# NOW (Notes On Work) Management Prompt

## Purpose

This prompt enables GitHub Copilot to create or update a minimal Notes On Work (NOW.md) that captures the essential project state for seamless context recovery. Use this when finishing work to ensure you (or anyone else) can instantly understand the project state later.

## Commands

### `/now`

Creates a new NOW.md file at the repository root by analyzing the codebase and recent conversation, or updates the existing NOW.md file with current state based on recent changes and conversation history.

## Instructions for Copilot

When the command is invoked:

1. **Analyze the current context**:

   - Review the conversation history for problems being solved, decisions made, and tasks discussed
   - Scan the codebase structure to understand the project
   - Check for test files and recent test results in common locations (tests/, reports/, .github/workflows/)
   - Look for existing NOW.md to determine if this is create or update
2. **Generate/Update NOW.md** with this EXACT structure (keep it under 500 words total):

```yaml
# NOW - Notes On Work
# Generated: <current UTC timestamp>
# Purpose: Minimal context for seamless project resumption

## META
canonical: true
updated: <YYYY-MM-DD>
version: <increment if updating, start at 0.1>

## STATE  
problem_solving: "<One sentence: what core problem does this repo solve?>"
current_focus: "<One sentence: what specific aspect/feature are we working on right now?>"
blocked_by: "<What's preventing progress? Be specific. Or 'nothing' if unblocked>"

## EVIDENCE
tests_passing:
  unit: <true | false | not_implemented>
  integration: <true | false | not_implemented>  
  evidence: "<path/to/last/test/report or 'none'>"
  last_run: "<YYYY-MM-DD or 'never'>"

## NEXT
immediate_task:
  intent: "<Single concrete task to do next - be specific>"
  done_when: "<Specific measurable outcome that proves completion>"
  evidence_required: ["<specific file/artifact that must exist when done>"]
  status: <pending | active | done>

## CONTEXT
key_decisions:
  - "<Only include decisions that would confuse someone reading the code>"
  - "<Keep list to 3 or fewer items>"
abandoned_approaches:
  - "<Approaches tried and rejected - prevent others from retrying>"
  - "<Keep list to 3 or fewer items>"
architecture: "<path/to/architecture/doc or 'none'>"
```

3. **Key Generation Rules**:

   - **problem_solving**: Extract from README, package.json description, or conversation. Must be one clear sentence.
   - **current_focus**: Based on most recent conversation/commits. What were we JUST working on?
   - **blocked_by**: Look for mentioned errors, missing dependencies, waiting states. Be specific enough that someone could unblock it.
   - **tests_passing**: Check for test files existence. Look for CI configs. Check reports/ or coverage/ directories.
   - **immediate_task**: Pull from conversation or last TODO mentioned. Must be a single, concrete action.
   - **done_when**: Must be objectively verifiable (e.g., "when X file exists", "when Y test passes", "when API returns Z")
   - **evidence_required**: Specific filename(s) that prove task completion
   - **key_decisions**: Only include non-obvious architectural choices that affect current development
   - **abandoned_approaches**: Only what's necessary to prevent someone from re-attempting failed solutions
4. **What NOT to include**:

   - Historical information that doesn't affect current development
   - Speculative future features beyond the immediate next task
   - Team/process information (who's working on what)
   - Detailed test breakdowns (just pass/fail/not_implemented)
   - Multiple next tasks (force single focus)
   - Explanations or justifications (just facts)
5. **Quality Checks**:

   - Total file must be <2000 tokens (~500 words)
   - Each field must be filled (use "none", "not_implemented", or "nothing" rather than leaving blank)
   - No markdown formatting beyond the structure shown
   - Dates in YYYY-MM-DD format
   - Paths relative to repo root

## Example Interaction

Developer: `/now`

Copilot response:

```
I'll update the NOW.md file based on our conversation and the current codebase state.

From our discussion, you were working on implementing a load test harness but discovered the test environment wasn't configured. The unit tests are passing but integration tests aren't implemented yet.

Here's the updated NOW.md:

[generates file following structure above]

The NOW has been updated. Key changes:
- Updated current_focus to reflect load testing work
- Set blocked_by to note the missing test environment configuration  
- Set immediate_task to unblock the test environment issue
- Added the load testing approach to abandoned_approaches since environment wasn't ready

You can now safely step away from this project. When you return, the NOW will immediately remind you that you need to configure the test environment before proceeding with load testing.
```

## Error Handling

If Copilot cannot determine certain information:

- For STATE fields: Make best inference from available context
- For EVIDENCE: Mark as "not_implemented" if no tests found
- For NEXT: Extract from conversation or set to "Review and plan next steps"
- For CONTEXT: Leave empty lists rather than guessing

## Remember

This document is for GitHub Copilot to consume first, humans second. Prioritize clarity and brevity over completeness. The goal is instant reorientation, not comprehensive documentation.
