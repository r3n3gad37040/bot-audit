# Ghost Protocol — Engineering Doctrine

**Version**: 1.0
**Date**: 2026-03-27
**Team**: Claude (Director), Hermes (Review), Codex (Research), Qwen+ (Generation)

---

## Stack

| Layer | Tech |
|---|---|
| Frontend | React 18 + Vite + MUI v5 |
| Backend | Node.js / Express, port 4444 |
| Database | SQLite (better-sqlite3) |
| Analysis Engine | Kali Docker container (`kali`) |
| Real-time | WebSocket (ws) |

**Ports**: Frontend 5175, Backend 4444
**Container**: `docker exec kali <cmd>` — all analysis tools run inside

---

## Analysis Tools (Kali Container)

| Tool | Path / Command | Purpose |
|---|---|---|
| radare2 | `r2` | Disassembly, binary analysis |
| rabin2 | `rabin2` | Metadata, symbols, functions |
| file | `file` | Magic byte detection |
| strings | `strings` | Printable string extraction |
| xxd | `xxd` | Hex dump |
| binwalk | `binwalk` | Embedded file carving |
| ssdeep | `ssdeep` | Fuzzy hashing |
| yara | `yara` | Rule-based pattern matching |
| die / diec | `diec` | Packer / compiler detection |
| pefile | Python `pefile` | PE header parsing |
| olevba | `olevba` | Office macro extraction |
| olefile | Python `olefile` | OLE document parsing |
| pdfid | `pdfid` | PDF structure profiling |
| pdf-parser | `pdf-parser` | PDF object extraction |
| floss | `floss` | Obfuscated string extraction (FLARE) |
| cryptography | Python `cryptography` | Authenticode / PKCS7 cert chain |

---

## Tool Runner Rules

All tools are registered in `backend/src/tools/toolRunner.js` under `TOOL_CATALOG`.

- **Strict whitelist**: only catalog-registered commands execute
- **r2 commands**: only entries in `ALLOWED_COMMANDS` Set are permitted
- **No shell expansion**: args built as arrays, never string-concatenated
- **Timeouts**: enforce per-tool; default 60s; never unlimited
- **Output**: always JSON where possible (`-j` flags on rabin2/r2)

When adding a new tool:
1. Register in `TOOL_CATALOG` with `cmd`, `buildArgs`, `timeout`, `parse`
2. Add backend action in `index.js` `TOOL_ACTIONS`
3. Add frontend module card in the relevant tab component
4. Hermes cross-validates before shipping

---

## Backend Action Pattern

```js
'binary-newtool': async ({ filePath, caseId, evidenceId, ...opts }) => {
  const result = await runTool('gp-newtool', { filePath });
  await saveFindings(db, evidenceId, caseId, result.findings, 'newtool');
  return result;
},
```

- Always save findings to DB via `saveFindings()`
- Return `{ findings, summary, ...metadata }` shape
- Binary library lookups: check existing evidence+findings, never re-trigger analysis

---

## Database Conventions

- All timestamps: `DATETIME DEFAULT (strftime('%Y-%m-%dT%H:%M:%fZ', 'now'))`
- Offsets in disasm_annotations: store as `INTEGER` (not text)
- Foreign keys: `evidence_id` + `case_id` on every findings table
- Migrations: run at startup in `initDb()` — additive only, no destructive ALTER

---

## Frontend Conventions

- **Monospace font** everywhere: `fontFamily: 'monospace'`
- **Color palette**: `#060b14` bg, `#0a0e1a` card, `#1e293b` border, `#475569` muted
- **Severity colors**: imported from `utils/buildFindingsHtml.js` → `SEV_COLORS`
- **No inline large data**: use pagination or virtualization (react-window) for lists > 100 items
- **Module cards**: uniform height, `RUN` button, progress indicator, findings count badge
- **Output tabs**: Strings | Disasm | Functions | Hex — consistent tab bar pattern

---

## Workflow

Follow **TEAM_DOCTRINE.md** for all feature work:

1. **Planning Loop** — draft → Hermes review → Codex research → final plan → Johnny approval
2. **Build** — implement per approved plan; no scope creep
3. **Verification Loop** — Hermes full adversarial review; no agent checks their own work
4. **Ship** — only after verification passes

**Cross-validation is mandatory.** A task is not done until Hermes (or another agent) has reviewed it.

---

## Security Rules

- No command injection: all subprocess args as arrays
- No shell=True equivalents in Python scripts
- File paths validated against case/evidence ownership before tool execution
- VT API key via environment variable, never hardcoded
- No user-controlled data in SQL without parameterized queries
