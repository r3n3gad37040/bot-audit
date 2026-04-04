# Team Stack Index
**Available operational frames for NEXUS. Load only when the mission requires it.**

Do not load all frames at once. Read this index, identify which frame(s) are load-bearing for the current task, then read only those files before proceeding.

---

## Always Active
| File | Purpose |
|------|---------|
| `ghost_protocol_brief.md` | **Load on every dispatch.** Operational posture, F3EAD loop, agent roles, mindset rules. |
| `ghost_protocol.md` | Full Ghost Protocol doctrine. Load at major operation start or full team re-frame only. |

---

## Domain Frames (Load When Relevant)
| File | Invoke When... |
|------|----------------|
| `intelligence.md` | Task involves OSINT, surveillance, network mapping, source analysis, or counterintelligence |
| `legal.md` | Task involves case law, filings, regulatory compliance, permits, appeals, or legal strategy |
| `ENGINEERING.md` | Task involves system architecture, infrastructure, code, or technical build decisions |
| `security.md` | Task involves threat assessment, physical/cyber security, risk analysis, or vulnerability research |
| `finance.md` | Task involves financial records, fraud investigation, asset tracing, or economic analysis |
| `research.md` | Task requires academic-style research, source verification, literature review, or fact synthesis |
| `environmental.md` | Task involves environmental law, land use, CEQA/NEPA, zoning, or ecological impact |
| `tactical.md` | Task requires operational planning, sequencing, resource allocation, or execution strategy |
| `intelligence.md` | Task involves structured intelligence production, pattern analysis, or adversarial assessment |
| `medical.md` | Task involves health, clinical data, pharmacology, or medical record analysis |
| `creative.md` | Task involves writing, visual design, narrative construction, or persuasive communication |
| `education.md` | Task involves training, knowledge transfer, curriculum, or explanatory content |

---

## Loading Protocol
When you determine a domain frame is needed, read the file **before** beginning the task — not during:
```
cat "/home/johnny/BOT-AUDIT/Team Stack Frames/<frame>.md"
```

Multiple frames can be stacked if the mission spans domains. Example: a land-use appeal requires `legal.md` + `environmental.md` + `intelligence.md` simultaneously.

Claude identifies which frames are active at Commander's Intent. All agents load the same frames.
