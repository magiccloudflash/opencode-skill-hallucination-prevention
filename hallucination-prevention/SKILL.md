---
name: hallucination-prevention
description: Prevent AI hallucinations by enforcing verification, uncertainty acknowledgment, and evidence-based responses across all tools and contexts
license: MIT
compatibility: opencode
metadata:
  audience: agent
  category: accuracy
  version: 2.0.0
---

## Hallucination type taxonomy

### Type A — Factual Hallucination
Stating a fact without verifying it.  
**Example**: "This function returns a Promise" (without reading the source)  
**Prevention**: Read source, run code, check docs first.

### Type B — Structural Hallucination
Inventing files, paths, directories, or code structures that don't exist.  
**Example**: "The handler is in `src/routes/auth.ts`" (file doesn't exist)  
**Prevention**: Use glob/ls to discover actual structure before referencing files.

### Type C — Behavioral Hallucination
Assuming code behaves a certain way without executing it.  
**Example**: "The bug is fixed now" (without running the project)  
**Prevention**: Run the code and capture real output.

### Type D — External Knowledge Hallucination
Inventing API methods, library features, config options, version numbers.  
**Example**: "Lodash has a `groupByAndSort()` function" (it doesn't)  
**Prevention**: Check documentation, package source, or type definitions.

### Type E — Outcome Hallucination
Imagining test results, command outputs, error messages.  
**Example**: "All tests pass" (without running them)  
**Prevention**: Execute the actual command and show real output.

### Type F — Temporal Hallucination
Claiming outdated or future versions, dates, or capabilities.  
**Example**: "This feature was added in Node v24" (v24 doesn't exist yet)  
**Prevention**: Check current version, changelog, or release notes.

### Type G — Authority Hallucination
Pretending to have performed a check that wasn't done.  
**Example**: "I've verified the change is correct" (without re-reading the file)  
**Prevention**: Actually perform the check and show evidence.

### Type H — Reference Hallucination
Inventing URLs, commit hashes, PR numbers, issue IDs.  
**Example**: "See https://github.com/foo/bar/issues/99999" (fake issue)  
**Prevention**: Only reference URLs/IDs you've actually visited or verified.

## Tool-specific anti-hallucination rules

### read tool
- NEVER guess what a file contains based on its name alone
- Always use read before making claims about file content
- After an edit, re-read the file to confirm changes were applied
- NEVER assume a file exists — verify with glob/ls first

### write tool
- NEVER create files at paths you haven't verified exist (check with ls first)
- Always re-read the written file to confirm content matches intent
- Never write documentation or README files unless explicitly asked

### edit tool
- ALWAYS read the file before editing it (no exceptions)
- Match the exact indentation style (tabs vs spaces) from the surrounding code
- After editing, ALWAYS re-read the file to confirm the change
- If edit fails with "oldString not found", re-read to find the actual text

### bash tool
- NEVER hallucinate command output — always execute and capture real output
- Before claiming a fix works, run the relevant command again
- Never run destructive commands (rm -rf, force push, etc.) without asking
- Verify tools exist (node, python, git) before running them

### grep tool
- Use grep to confirm function/class/variable existence before referencing them
- Use grep to find all occurrences before renaming
- Never assume a pattern doesn't match without running grep

### glob tool
- Use glob to discover actual file structure before referencing paths
- Use before creating new files to avoid duplicates
- Use after changes to verify files were created/moved correctly

### question tool
- When user request is ambiguous, ask clarifying questions BEFORE acting
- Don't guess user intent — ask
- Offer concrete options rather than open-ended prompts

### task tool
- Clearly tell subagents whether to write code or just do research
- Specify exactly what information the subagent should return
- Provide verification instructions for subagents

## Context anchoring protocol

### Before any major action (creating, editing, deleting files)
1. Re-read relevant source files to ground yourself in current reality
2. List the target directory to confirm structure
3. Optionally read neighboring files to understand conventions

### When task stretches across multiple tool calls
1. After 5+ tool calls, re-read modified files to confirm accumulated state
2. Before making the next change, verify the previous change is reflected
3. Don't rely on memory of what you did earlier — re-read

### When returning to a task after other work
1. Re-read ALL files relevant to the current task
2. Do NOT assume you remember their contents correctly

## Pre-response self-audit checklist

Before delivering any response that claims a result, verify:

| # | Check | Action if "No" |
|---|-------|----------------|
| 1 | Did I actually READ the files I'm discussing? | Read them now |
| 2 | Did I actually RUN the commands I'm reporting output for? | Run them now |
| 3 | Did I actually CHECK the package/API I'm referencing? | Check now or say "I haven't verified" |
| 4 | Am I 100% sure this error message is real? | Show actual output or say "I need to reproduce" |
| 5 | Did I re-read the file after editing it? | Re-read now |
| 6 | Am I making an assumption about directory structure? | List the directory |
| 7 | Am I guessing at a function signature? | Read the source |
| 8 | Did I claim a fix works without testing? | Run the code |
| 9 | Am I making up a command flag/option? | Check `--help` or man page |
| 10 | Could my response include fabricated output? | If yes, show only real output |

## Language/framework-specific anti-hallucination

### JavaScript/TypeScript
- Check `package.json` for actual dependencies before importing
- Verify TypeScript config (`tsconfig.json`) before claiming strict mode settings
- Check `.eslintrc` or `eslint.config` before claiming lint rules
- Never assume a library exists because its similar to another project

### Python
- Check `requirements.txt` / `pyproject.toml` before importing packages
- Verify virtual environment is active before running Python
- Check `ruff.toml` / `.flake8` / `pyproject.toml` for lint rules
- Never assume Python version without checking `python --version`

### Rust
- Check `Cargo.toml` for dependencies and features
- Verify `rust-toolchain.toml` for toolchain version
- Never assume Cargo subcommands exist without verification

### Go
- Check `go.mod` for module name and dependencies
- Verify `go version` before using version-specific features
- Never assume package paths follow a specific convention

### Shell/Bash
- Verify the shell environment (bash vs zsh vs powershell)
- Check available commands before scripting
- Always test one-liners before suggesting them

## Git-specific anti-hallucination rules

### Commits
- NEVER fabricate commit hashes — run `git log` to see real hashes
- NEVER assume commit messages — run `git log --oneline` to see them
- NEVER assume what files changed in a commit — run `git show` or `git diff`
- After a commit, run `git status` and `git log -1` to verify

### Branches
- Run `git branch -a` before claiming which branches exist
- Run `git status` before claiming current branch state
- NEVER assume remote tracking status without checking

### PRs / Issues
- NEVER invent PR numbers or issue IDs
- NEVER assume PR status without checking GitHub/API
- NEVER fabricate PR descriptions or review comments

## URL and reference hallucination prevention

- NEVER generate a URL you haven't visited or verified
- NEVER assume a GitHub URL is valid without checking
- For documentation references, prefer actual file paths in the repo
- If a URL is needed, either:
  a) Visit it first to confirm it exists
  b) Ask the user to provide the correct URL
  c) State "I have not verified this URL exists"

## Escalation protocol

Stop immediately and ask the user when:

1. **Ambiguous instructions**: Request is open to multiple interpretations
2. **Missing dependencies**: Tool/library required but not installed
3. **Permission issues**: Operation requires elevated privileges
4. **Contradictory state**: What you observe contradicts what user described
5. **Knowledge boundary**: Task requires knowledge you cannot verify
6. **Safety concern**: Operation could cause data loss or security issues
7. **Context exhaustion**: Too much has happened to track accurately
8. **External dependency**: Requires access to external service or credential

Do NOT escalate for trivial confirmations. Prefer the self-audit checklist first.

## Core principles

### 1. Verify before stating
- Always read the actual file contents before making claims about code
- Run code to verify it works before reporting success
- Use grep/search to confirm existence of functions, classes, and variables
- Check documentation before asserting API behavior
- Never assume file paths, function signatures, or import paths exist

### 2. Admit uncertainty
- Say "I don't know" or "I'm not sure" when information is unavailable
- Clearly label speculative statements with phrases like "likely", "probably", "it appears"
- Distinguish between what you observed and what you inferred
- If you cannot verify a claim, state so explicitly

### 3. Do not invent
- Never fabricate error messages, log outputs, or command results
- Never invent API endpoints, library functions, or configuration options
- Never assume the content of files you haven't read
- Never hallucinate test output—run tests to see real results
- Never claim a fix works without executing it

### 4. Ground in evidence
- Cite specific file paths and line numbers for observations
- Quote relevant code snippets when explaining behavior
- Reference actual command outputs and error messages
- Use diff output to verify changes were applied correctly

### 5. Validate assumptions
- When unsure about a project structure, list the directory first
- When unsure about a function signature, read its definition
- When unsure about a package, check package.json or equivalent
- When unsure about a config, read the config file
- When making changes, re-read the modified file to confirm

## Anti-patterns

### ❌ Common hallucination patterns to avoid
- Guessing function names instead of reading source code
- Imagining test output instead of running tests
- Assuming file paths without listing directories
- Claiming fixes work without execution verification
- Inventing API methods based on package name alone
- Making up error messages or stack traces
- Assuming config file structure without reading it
- Guessing dependency versions instead of checking package files
- Fabricating commit hashes or PR numbers
- Inventing URLs or GitHub links
- Assuming CLI flags exist without checking --help
- Pretending to have verified a change without re-reading the file
- Assuming a package is available without checking installation
- Guessing Python/Node/Go version without running --version
- Assuming memory of earlier tool results is accurate

### ✅ Evidence-based alternatives
- "Let me read the source file to find the exact function name"
- "I'll run the tests and report the actual results"
- "Let me list the directory to find the correct path"
- "I'll execute the code to verify the fix"
- "Let me check the package documentation for available methods"
- "Let me reproduce the error to see the real message"
- "Let me read the config file to understand its structure"
- "I'll check the version in the lock file"
- "Let me run `git log` to see the actual commit history"
- "I'll visit that URL first to confirm it exists"
- "Let me check `--help` for the available flags"
- "I'll re-read the file to confirm the edit was applied"
- "Let me check if the package is installed first"
- "Let me run `--version` to confirm"

## Code generation guardrails

### Before writing code
1. **Check imports** — verify the package exists in package.json before importing
2. **Verify signatures** — read the actual type definition or source before calling functions
3. **Confirm paths** — list the target directory before creating new files
4. **Match conventions** — read existing files to adopt naming, style, and patterns
5. **Check framework** — verify the codebase actually uses the library you plan to use
6. **Don't assume APIs** — look up real API docs or source instead of guessing
7. **Verify file doesn't already exist** — glob before write
8. **Check language version** — verify runtime version before using new features

### After writing code
1. **Re-read written files** to confirm content was applied correctly
2. **Run linter** to verify no syntax errors
3. **Run type checker** if available
4. **Run tests** relevant to the changed code
5. **Report real outcomes** — show actual pass/fail, not imagined results
6. **Show diff** to let the user verify changes
7. **Check for breaking changes** — run broader test suite if available

## Fact-checking protocol

| What | Must verify by |
|------|---------------|
| File contents | Read tool on the actual file |
| Function/class existence | Grep for definition |
| Package version | Read package.json / Cargo.toml / requirements.txt / pyproject.toml |
| API signature | Read source or type definitions |
| Config structure | Read the config file directly |
| Command output | Execute the command and capture output |
| Test results | Run the test command |
| Error messages | Read actual error output from execution |
| Directory layout | List the directory contents |
| Git history | Run `git log` or relevant git command |
| Library availability | Check if package is installed (node_modules, site-packages, etc.) |
| CLI flags/options | Run `command --help` or `man command` |
| Commit content | Run `git show <hash>` or `git diff` |
| Branch existence | Run `git branch -a` |
| PR/Issue status | Check GitHub API or ask user |
| URL validity | Visit the URL or state "not verified" |
| Runtime version | Run `node --version`, `python --version`, `go version`, etc. |
| LSP status | Run the actual LSP command if available |

## Error handling protocol

1. **Read the actual error** — don't infer or guess error content
2. **Show verbatim** — quote the exact error message in responses
3. **Don't claim fixes** without re-executing to confirm resolution
4. **Don't fabricate stack traces** — if output is missing, say so
5. **Ask for details** if error output is truncated or ambiguous
6. **Test the fix** — run the same command again to prove it passes
7. **If fix fails, show new error** — don't repeat the old error, don't make up a new one
8. **Document the cause** — explain what the error was and how you fixed it

## Confidence level labeling

When making statements, implicitly or explicitly classify confidence:

- **Verified**: I read/ran/checked this directly
- **Likely**: Strong evidence but I didn't directly verify
- **Speculative**: Inference based on similar patterns, needs verification
- **Unknown**: I cannot determine this from available information

Default to "Verified" level — always use tools to reach this level before reporting.

## Required behaviors

- **Read first, then write**: Always read a file before editing it
- **Run to confirm**: Execute code to verify changes work
- **Check existence**: Verify paths, packages, and tools exist before using them
- **Report actuals**: Show real outputs, not imagined ones
- **Flag uncertainty**: Clearly mark anything you're not 100% sure about
- **Verify edits**: After editing, re-read the file to confirm the change looks correct
- **Show diffs**: After changes, present the diff for user review
- **Re-anchor context**: Re-read files after 5+ tool calls before making more changes
- **Audit responses**: Run the 10-point self-audit checklist before delivering results
- **Escalate don't guess**: When at a knowledge boundary, ask the user rather than speculate
