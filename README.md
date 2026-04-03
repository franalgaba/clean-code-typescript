# 🧹 Clean Code TypeScript — Claude Skill

A Claude skill that reviews and refactors TypeScript code following [Clean Code](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) principles by Robert C. Martin, adapted for TypeScript.

Paste your TypeScript code, ask for a review, and get back a structured report with every violation identified — plus a fully refactored version of your code.

## What it does

When triggered, the skill:

1. **Analyses** your TypeScript/TSX code against 40+ Clean Code rules across 10 categories
2. **Produces a report** with an issues table grouped by category, severity ratings, and explanations
3. **Refactors** the entire code to resolve all identified issues while preserving original behaviour

### Example output

```
## Clean Code Review

### Summary
This snippet has 8 violations across 3 categories...

### Issues

#### Variables
| # | Rule              | Location     | Issue                          | Severity |
|---|-------------------|--------------|--------------------------------|----------|
| 1 | Use searchable names | `const d = 86400000` | Magic number with no context | 🔴       |

#### Functions
| # | Rule              | Location     | Issue                          | Severity |
|---|-------------------|--------------|--------------------------------|----------|
| 2 | Don't use flags   | `proc(arr, f: boolean)` | Boolean makes function do two things | 🔴 |

### Refactored Code
// ... cleaned up version

### Key Changes
- Replaced magic number with named constant
- Split flag-driven function into two focused functions
- ...
```

### Severity levels

| Icon | Level    | Meaning                                                     |
|------|----------|-------------------------------------------------------------|
| 🔴   | **High**   | Actively harms readability or maintainability             |
| 🟡   | **Medium** | Worth fixing but not blocking                             |
| 🟢   | **Low**    | Style nit or minor improvement                            |

## Rules covered

The skill checks against rules in these categories:

- **Variables** — meaningful names, searchable names, no mental mapping, no unneeded context, enums, defaults
- **Functions** — single responsibility, 2 or fewer args, no flags, no side effects, functional over imperative, encapsulated conditionals, no dead code
- **Objects & Data Structures** — getters/setters, private members, immutability, type vs interface
- **Classes** — small classes, high cohesion, composition over inheritance, method chaining
- **SOLID** — SRP, OCP, LSP, ISP, DIP
- **Testing** — TDD laws, F.I.R.S.T. rules, single concept per test, intention-revealing names
- **Concurrency** — promises over callbacks, async/await over promise chains
- **Error Handling** — always use Error, never ignore caught errors or rejected promises
- **Formatting** — consistent capitalization, caller/callee proximity, organized imports, path aliases
- **Comments** — self-explanatory code, no commented-out code, no journal comments, no positional markers

Full ruleset based on [clean-code-typescript](https://github.com/labs42io/clean-code-typescript) by labs42io.

## Installation

Download the `.skill` file from [Releases](../../releases) and drag it into a Claude conversation, or add it through your Claude settings.

### Manual installation

Clone this repo into your Claude skills directory:

```bash
git clone https://github.com/<your-username>/clean-code-ts.git /path/to/skills/user/clean-code-ts
```

## Skill structure

```
clean-code-ts/
├── SKILL.md                                  # Main skill instructions
├── README.md                                 # This file
├── references/
│   ├── rules.md                              # Condensed ruleset (quick lookup)
│   └── clean-code-typescript-full.md         # Complete source with all examples
└── evals/
    └── evals.json                            # Test cases for skill validation
```

## Trigger phrases

The skill activates when you ask Claude to:

- *"Review this TypeScript code"*
- *"Clean up this class"*
- *"Refactor this for readability"*
- *"Audit my TS code"*
- *"What do you think of this code?"* (with TypeScript pasted)
- *"Improve this TypeScript"*

It does **not** trigger for pure formatting requests (e.g. "run Prettier"), non-TypeScript languages, or runtime debugging unrelated to code quality.

## Customisation

The skill handles several edge cases out of the box:

- **Already-clean code** — gives a short positive summary instead of manufacturing issues
- **Very long files (300+ lines)** — focuses on the top 15–20 most impactful issues
- **Framework-specific code** (React, Angular, NestJS) — respects idiomatic patterns
- **Review only** — if you ask for just a review, it skips the refactored code section
- **Refactor only** — if you ask for just a refactor, it skips the issues table

## Credits

Rules adapted from [clean-code-typescript](https://github.com/labs42io/clean-code-typescript) by [labs42io](https://github.com/labs42io), which itself is inspired by [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript) by [Ryan McDermott](https://github.com/ryanmcdermott), based on Robert C. Martin's [*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882).

## License

MIT