# OpenCode Skill: Hallucination Prevention

> An OpenCode agent skill that prevents AI hallucinations by enforcing verification, uncertainty acknowledgment, and evidence-based responses.

English | [中文](./README_CN.md) | [中文 (繁體)](./README_TW.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | [Italiano](./README_IT.md) | [Español](./README_ES.md) | [Español (Cuba)](./README_CU.md) | [Português](./README_PT.md) | [Português (Brasil)](./README_BR.md) | [العربية](./README_AR.md) | [हिन्दी](./README_HI.md) | [اردو](./README_UR.md) | [ไทย](./README_TH.md) | [فارسی](./README_FA.md) | [Türkçe](./README_TR.md) | [Polski](./README_PL.md) | [Nederlands](./README_NL.md) | [Svenska](./README_SV.md) | [Ελληνικά](./README_EL.md)

## Features

- **Verify before stating** — read files, run code, check docs before making claims
- **Admit uncertainty** — clearly label speculation vs. verified facts
- **Do not invent** — never fabricate errors, APIs, test results, or fixes
- **Ground in evidence** — cite file paths, line numbers, and actual output
- **Self-check flow** — 5-step verification checklist before every response
- **Code generation guardrails** — pre/post write verification
- **Error handling protocol** — read real errors, show verbatim, verify fixes

## Installation

### Option 1: Clone this repo

```bash
git clone https://github.com/<your-org>/opencode-skill-hallucination-prevention.git
```

Then copy the skill to your global opencode config:

```bash
# macOS / Linux
cp -r hallucination-prevention ~/.config/opencode/skills/

# Windows PowerShell
Copy-Item -Recurse hallucination-prevention "$env:USERPROFILE\.config\opencode\skills\"
```

### Option 2: Manual install

Create the skill directory and copy `SKILL.md`:

```bash
# macOS / Linux
mkdir -p ~/.config/opencode/skills/hallucination-prevention

# Windows PowerShell
mkdir -p "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention"
```

Then place [`hallucination-prevention/SKILL.md`](hallucination-prevention/SKILL.md) into that directory.

### Option 3: As a global rule (auto-load every session)

Copy the enhanced rules to your global AGENTS.md:

```bash
# macOS / Linux
cat hallucination-prevention/SKILL.md >> ~/.config/opencode/AGENTS.md

# Windows PowerShell
Add-Content -Path "$env:USERPROFILE\.config\opencode\AGENTS.md" -Value (Get-Content "hallucination-prevention\SKILL.md")
```

Or use the standalone [`AGENTS.md`](AGENTS.md) provided in this repo.

## Usage

The skill is automatically discovered by OpenCode. The agent will see it listed in available skills and load it when relevant.

To load it explicitly during a session:

```
The agent can use: skill({ name: "hallucination-prevention" })
```

### Permission configuration

In your `opencode.json`, you can control access:

```json
{
  "permission": {
    "skill": {
      "hallucination-prevention": "allow"
    }
  }
}
```

For per-agent override:

```json
{
  "agent": {
    "plan": {
      "permission": {
        "skill": {
          "hallucination-prevention": "allow"
        }
      }
    }
  }
}
```

## Structure

```
opencode-skill-hallucination-prevention/
├── README.md
├── LICENSE
├── opencode.json              # Example global config
├── AGENTS.md                  # Standalone global rules (auto-load version)
└── hallucination-prevention/
    └── SKILL.md               # The skill definition
```

## Skill contents

| Module | Description |
|--------|-------------|
| **Core principles** | 5 principles: verify, admit uncertainty, don't invent, evidence, validate |
| **Anti-patterns** | Common hallucination patterns with ❌/✅ examples |
| **Code generation guardrails** | Pre-write and post-write verification checklists |
| **Fact-checking protocol** | Table of what to verify and how |
| **Error handling protocol** | 6-step honest error handling workflow |
| **Confidence level labeling** | 4-level confidence system (Verified → Unknown) |
| **Self-check flow** | 5-question pre-response checklist |
| **Required behaviors** | Read first, run to confirm, check existence, show diffs |

## License

MIT
