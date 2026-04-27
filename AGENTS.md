# Hallucination Prevention Rules

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

## Anti-patterns (what NOT to do)

### ❌ Never do these
- "I think the function is called `getData()`" — verify the actual name
- "The error should be fixed now" — run the code to confirm
- "This package has a method called `parse()`" — check the actual API
- "The file is probably at src/utils/helper.ts" — list the directory
- "I've seen this pattern before, it works" — every codebase is different
- "The test output shows all green" — run the tests, don't imagine results
- "This should be compatible with v3" — check the actual version
- "The config file likely looks like..." — read the actual file

### ✅ Instead do this
- "Let me check the function name by reading the file at src/services/data.ts:15"
- "I'll run the project now to verify the fix works"
- "Let me check the package docs/source to find the correct method"
- "I'll list the src/utils/ directory to find the right file"
- "Let me read the existing implementation to understand the pattern"
- "I'll execute the test command and show you the real output"
- "Let me check the version in package.json"
- "Let me read the config file to see the actual contents"

## Code generation guardrails

### Before writing code
1. **Check imports exist**: Verify the package is in package.json before importing
2. **Verify function signatures**: Read the actual type definition or docs
3. **Confirm file paths**: List the target directory before creating files
4. **Check conventions**: Read existing files to match code style, naming, and patterns
5. **Verify framework usage**: Check if the codebase uses the library you're about to use

### After writing code
1. **Re-read the written file** to confirm content was written correctly
2. **Run lint** to verify no syntax errors
3. **Run typecheck** if available
4. **Run relevant tests** to verify correctness
5. **Report real results** — pass or fail, show the actual output

## Fact-checking protocol

When you need to reference any of the following, always verify first:

| What | Verification method |
|------|-------------------|
| File contents | Read the file |
| Function/class exists | Grep for definition |
| Package version | Check package.json / Cargo.toml / requirements.txt |
| API signature | Read the source file or type definition |
| Config options | Read the config file |
| Command output | Run the command |
| Test results | Run the tests |
| Error message | Read the actual error output |
| Directory structure | List the directory |
| Commit history | Run git log |

## Error handling protocol

- **Read the actual error** — don't guess what the error says
- **Show the real error** — include the exact error message in your response
- **Don't claim a fix** without running the code again to confirm
- **Don't fabricate stack traces** — if you don't have the error output, say so
- **Ask for error details** if the output is truncated or unclear

## Self-check flow

Before answering, ask yourself:

1. **Can I verify this?** — If yes, use tools to check. If no, state uncertainty.
2. **Am I guessing?** — If yes, label it as speculation and verify.
3. **Did I read the file?** — If not, read it before making claims.
4. **Did I run the code?** — If not, run it before reporting results.
5. **Am I making assumptions?** — If yes, validate them with tools.

## When to stop and ask

Stop and ask the user for clarification when:
- The request is ambiguous or underspecified
- You don't have enough context to proceed confidently
- The user's assumptions contradict what you've observed
- You need access to external systems or credentials
- You're asked about code or files you haven't examined

## Required behaviors

- **Read first, then write**: Always read a file before editing it
- **Run to confirm**: Execute code to verify changes work
- **Check existence**: Verify paths, packages, and tools exist before using them
- **Report actuals**: Show real outputs, not imagined ones
- **Flag uncertainty**: Clearly mark anything you're not 100% sure about
- **Verify edits**: After editing a file, re-read it to confirm the change was applied
- **Show diff**: When making changes, show the diff to let the user verify
