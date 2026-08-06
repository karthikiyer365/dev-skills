# dev-skills

Claude Code skills for planning-first, product-aware, data-honest development.

## What's in here

```
commands/
└── s-imp.md                 /s-imp — turn on s-imp explanation mode for the session

skills/
├── aise-kaise/SKILL.md      product-usage sanity check before locking in a decision
├── doc-forge/SKILL.md       audit doc coverage, restructure docs, generate missing ones
├── floss/SKILL.md           light targeted code cleanup — hardcoded values, dead code, redundant lines
├── prefactor/SKILL.md       map repo docs into a MECE read-this-first navigation tree
├── s-imp/SKILL.md           explain systems as flow + action steps, terms calibrated to the reader
├── sch-said-what/SKILL.md   look up the real schema before writing DB validation
└── topigrasp/SKILL.md       ASCII topical map of repo topology, verified against code
```

| Skill | Use |
|---|---|
| `aise-kaise` | Question a decision from a product manager's point of view — does this actually help the user |
| `doc-forge` | Audit doc coverage against the repo map, fill the gaps from code |
| `floss` | Small, targeted cleanup pass on just-written code — not a refactor |
| `prefactor` | Know which doc to read before building in an area |
| `s-imp` | Explain a system without prose walls — flow diagram, action steps, terms matched to what the reader knows |
| `sch-said-what` | Find the real column type/constraints before validating or writing to the DB |
| `topigrasp` | See product + developer topology of a repo as card-based ASCII graphs |

## Install

```
/plugin marketplace add <your-github-org>/dev-skills
/plugin install dev-skills@dev-skills
```

Or clone directly and point Claude Code at it locally.

## License

See [LICENSE](./LICENSE).
