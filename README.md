# salesforce-cnpj-migration

> GitHub Copilot agents and prompts to migrate any Salesforce project to support the **new Brazilian CNPJ alphanumeric format** (IN RFB nº 2.229/2024, effective July 2026).

---

## What this does

The new CNPJ format issues 14-character alphanumeric identifiers (`[A-Z0-9]{12}[0-9]{2}`). Existing Salesforce projects that validate or process CNPJs must be updated to accept the new format — or they will **reject valid records starting July 2026**.

This toolkit provides:
- **Automatic discovery** of all impacted files in your project
- **Step-by-step migration** with diff preview and confirmation before every change
- **Technical reference** for the correct algorithm (Módulo 11 with ASCII-48 values)

---

## Files included

```
.github/
├── agents/
│   ├── cnpj-alphanumerico.agent.md     ← CNPJ technical reference (universal — do not change)
│   └── salesforce-apex.agent.md        ← Apex coding conventions (adapt to your project)
└── prompts/
    ├── salesforce-project.prompt.md    ← Search patterns + impact map (sections 1-4 static, section 5 generated at runtime)
    └── cnpj-migration-generic.prompt.md ← Migration orchestrator (universal — do not change)
```

---

## Installation

**Copy the `.github/` folder into the root of your Salesforce project.**

```
your-salesforce-project/
└── .github/          ← copy this entire folder here
    ├── agents/
    └── prompts/
```

Requirements:
- VS Code with GitHub Copilot (agent mode)
- Copilot version that supports `.agent.md` and `.prompt.md` customization files

---

## Adapting to your project (optional but recommended)

Edit `.github/agents/salesforce-apex.agent.md` and replace the placeholder sections with your project's actual:
- Architecture description
- Naming conventions table
- PMD / quality rules

The other 3 files work as-is for any Salesforce project.

---

## How to use

1. Open VS Code in your Salesforce project
2. Open the Copilot Chat panel in **Agent mode**
3. Type `/cnpj-migration-generic`

The agent will:

```
FASE 0  → reads search patterns from salesforce-project.prompt.md (sections 1-4)
        → scans your entire project for CNPJ-related files
        → writes the impact map to section 5 of salesforce-project.prompt.md
        → shows the map → waits for your confirmation

FASE 1  → presents the ordered execution plan (CRITICAL → HIGH → MEDIUM)
        → waits for your confirmation to start

FASE 2  → for each file:
            reads the real file content
            analyzes what needs to change
            shows a diff (BEFORE / AFTER)
            waits for your [sim / não / pular]
            applies only after confirmation

FASE 3  → final checklist with items requiring manual review
          (Duplicate Rules, Matching Rules, Flows)
```

**No file is ever changed without you seeing the diff and confirming.**

---

## How the files connect

```
You call /cnpj-migration-generic
        │
        ├── reads → cnpj-alphanumerico.agent.md   (CNPJ rules: algorithm, format, regex)
        ├── reads → salesforce-apex.agent.md       (your project's coding conventions)
        ├── reads → salesforce-project.prompt.md   (search patterns — sections 1-4)
        │                   ↓
        │           scans project files
        │                   ↓
        └── writes → salesforce-project.prompt.md  (section 5: impact map)
                            ↓ you confirm
                    runs migration file by file
```

---

## CNPJ New Format — Quick Reference

| Part | Positions | Type |
|---|---|---|
| Raiz | 1–8 | `[A-Z0-9]` |
| Ordem | 9–12 | `[A-Z0-9]` (Matriz = always `0001`) |
| DV | 13–14 | `[0-9]` (always numeric) |

Full schema: `[A-Z0-9]{12}[0-9]{2}`

Real examples (official RFB simulator):
```
CT.G3P.N2M/0001-22  ← MATRIZ
CT.G3P.N2M/9EHH-87  ← Filial
CT.G3P.N2M/VR52-03  ← Filial
```

Old numeric CNPJs remain **100% valid forever**. Systems must accept both formats simultaneously.

---

## Contributing

If you find a new CNPJ-related pattern in a Salesforce project that isn't covered by sections 1–4 of `salesforce-project.prompt.md`, open a PR adding the pattern. Do not remove existing entries.

---

## License

MIT
