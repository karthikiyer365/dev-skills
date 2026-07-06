# dev-skills

Claude Code skills for planning-first, product-aware, data-honest development.

## What's in here

```
skills/
├── aise-kaise/SKILL.md      product-usage sanity check before locking in a decision
├── floss/SKILL.md           light targeted code cleanup — hardcoded values, dead code, redundant lines
└── sch-said-what/SKILL.md   look up the real schema before writing DB validation
```

| Skill | Use |
|---|---|
| `aise-kaise` | Question a decision from a product manager's point of view — does this actually help the user |
| `floss` | Small, targeted cleanup pass on just-written code — not a refactor |
| `sch-said-what` | Find the real column type/constraints before validating or writing to the DB |

## Install

```
/plugin marketplace add <your-github-org>/dev-skills
/plugin install dev-skills@dev-skills
```

Or clone directly and point Claude Code at it locally.

## License

See [LICENSE](./LICENSE).
