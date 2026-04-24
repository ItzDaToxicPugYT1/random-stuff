---
name: plan
description: >-
  Runs a structured, stack-agnostic project kickoff: discovery, spaced numbered
  questions, a mandatory BLUEPRINT.md with fixed sections, user sign-off before
  implementation, research logged to research.txt, folder layout, milestones,
  and definition of done. Use when starting or replanning any project (web,
  mobile, game, plugin, ops, docs-only), when the user asks for a blueprint,
  phased plan, or "how should we start this project."
---

# Generic project planning (any project type)

This skill defines a **repeatable workflow** so an assistant can start **any** project in an organized way. The **BLUEPRINT** (`BLUEPRINT.md`) is the contract: nothing that depends on the plan (scaffolding, large refactors, multi-file features) proceeds until the user accepts it.

**Chat vs files:** Follow workspace **responses** rules for *chat* (brevity). **Plans, questions, and BLUEPRINT content** live in files or structured numbered blocks — length there is **not** “essay chat”; it is the deliverable.

**Research:** While executing this skill, **Phase 5** explicitly includes research. Appending to `research.txt` in the **project root** is allowed **after** the user has **accepted the BLUEPRINT** and agreed to continue (e.g. “continue,” “LGTM,” “approved,” “do research”). That counts as explicit consent for **this workflow’s** research step. If the user skips straight to “just code,” fall back to the **edge-case** path (mini-plan + confirm).

---

## Artifact map

| Artifact | Location | When |
|----------|----------|------|
| **BLUEPRINT.md** | Project root (or user-specified root) | After Phase 2; revised until accepted |
| **research.txt** | Project root | Phase 5 append; English; categorized |
| **Optional:** `PROJECT_NOTES.md` | If user wants running decisions | Any time |

Default blueprint filename: **`BLUEPRINT.md`**. If the user prefers `blueprint.md` or `docs/BLUEPRINT.md`, use that and state it once in the blueprint header.

---

## Phase 0 — Preconditions

1. **One goal per thread** — If the user mixes several unrelated products, force a choice or split into separate blueprints (see Edge cases).
2. **Identify field** — Greenfield (empty/new), brownfield (existing repo), or hybrid.
3. **Do not** invent secrets, API keys, or real credentials — use **placeholders** and `.env.example` patterns in the blueprint only.

---

## Phase 1 — Discovery (short)

**Objective:** Capture a rough intent without locking stack.

**Ask for (adapt to context):**

- What are we building, in one breath?
- Who is it for (you, team, public users)?
- Hard constraints: deadline, hosting, languages you refuse, compliance?
- Is there an existing repo or folder to work in?

**Output (in chat, short):** 2–4 lines summarizing goal + constraints + greenfield/brownfield.

---

## Phase 2 — Structured questions

**Objective:** Replace guesswork with decisions before the BLUEPRINT exists.

### Rules

1. **Mix:** roughly **half open** (specific) and **half yes/no** (or forced choice). Total **8–12** questions is typical; complex projects may go to **15** with user OK.
2. **Format:** **One question per numbered item.** Blank line **between** every question. Never run them together in one paragraph.
3. **Spacing example (good):**

```text
1. Who can access v1 — only admins or all signed-in users?

2. What is the single most important user action we must ship first?

3. Should data live only on-device, only server, or both?

4. Do you need offline support in v1?
```

4. **Bad:** `1. … 2. … 3. …` in one line.

5. After answers: optionally **one** short clarification round (3–5 questions) if something critical is still unknown.

**Output:** Synthesized **Answers summary** bullet list (for pasting into §2 of the BLUEPRINT).

---

## Phase 3 — BLUEPRINT.md (mandatory)

**Objective:** Single source of truth for scope, structure, order, and risks.

**Create or overwrite `BLUEPRINT.md` at the chosen root** with **exactly these sections in this order.** Do not skip section numbers. Use `N/A` with one line why if not applicable.

### Section 1 — Project summary

- **Name / working title**
- **One-paragraph description** (here a short paragraph **is** allowed — it is the blueprint body, not chat)
- **Target audience**
- **Greenfield | brownfield | hybrid** + link/path to repo if brownfield

### Section 2 — Requirements

- **Goals** (bullet list)
- **Non-goals** (what we explicitly will not do in v1)
- **Priorities** (P0 / P1 / P2)
- **User answers** from Phase 2 (condensed bullets)

### Section 3 — Stack and tooling

- **Languages, frameworks, runtimes** — or **TBD** with decision criteria and options
- **Build, test, lint, format** (as applicable)
- **Hosting / deploy target** (or TBD)
- **Version control assumptions** (branch strategy if known)

*If stack is TBD:* state **what must be decided** before implementation and **who decides** (user vs team).

### Section 4 — Architecture (conceptual)

- **Major components** (boxes) and **data flow** (arrows in text or mermaid)
- **External services** (auth, payments, email, APIs)
- **Data storage** (none, files, SQL, document, blob) — conceptual only unless user wants schemas

### Section 5 — Repository and folder layout

**Greenfield:** Proposed **directory tree** (markdown code block) + **one line per top-level folder** explaining purpose.

**Brownfield:** **Current** tree summary (or “key paths only”) + **delta**: new folders/files to add, moves, deprecations.

**Mono-repo:** Root overview + **subsection per package** (`apps/…`, `packages/…`).

**Non-code projects (design, ops, pure docs):** Replace “folder layout” with **document outline** — list of files/sections to create under `docs/` or equivalent.

### Section 6 — User journeys / scenarios

At least **3** concrete scenarios, **role → action → outcome** format, e.g.:

- **Admin:** …
- **End user:** …
- **System / cron / webhook:** …

Adapt labels to the domain (e.g. **Player**, **Moderator**, **API consumer**).

### Section 7 — Milestones and implementation order

- **Ordered phases** (M1, M2, M3…) with **exit criteria** each
- **Dependency order** (what blocks what)
- **Suggested first PR / first slice** (smallest shippable increment)

### Section 8 — Research and references

- **Topics that need verification** (APIs, licenses, quotas, breaking changes)
- **Links already known** (official docs, repos)
- **Pointer:** “Post–approval research log: `research.txt` (Phase 5)”

### Section 9 — Risks, unknowns, decisions

- **Risks** + mitigation
- **Open questions** still unresolved
- **Decision log** (date optional): decision | choice | rationale (table or bullets)

### Section 10 — Security, privacy, compliance (lightweight)

- **Secrets handling** (env vars, vaults — no real values)
- **PII / data retention** if relevant
- **AuthN/Z** intent (none, basic, SSO, …)

### Section 11 — Definition of done (v1)

Checklist the user can tick, e.g.:

- [ ] Core P0 scenarios work end-to-end
- [ ] Tests or manual test script documented
- [ ] README / run instructions
- [ ] No secrets in repo
- [ ] License / third-party notices if dependencies exist

### Section 12 — Blueprint changelog

- Version **0.1** — initial draft date
- Subsequent rows when blueprint is revised

---

## Phase 4 — Blueprint review (gate)

**Rule:** **Do not** start implementation that **depends** on the plan (new modules, large file creation beyond the blueprint, feature code) until the user **explicitly accepts** the blueprint.

**Accept signals:** “LGTM,” “approved,” “good,” “ship it,” “let’s build,” thumbs-up, etc.

**If changes requested:**

- Update **BLUEPRINT.md**; bump **changelog** in §12.
- Re-present **only** the diff summary in chat (short) + “ready again?”

**If user insists “skip blueprint, just code”:** Write **mini blueprint** (Goals, Non-goals, First slice, Risks) in ≤15 lines inside `BLUEPRINT.md` or a `MINI_PLAN.md`, then **one** confirm line. Still no big implementation without that confirm.

---

## Phase 5 — Research

**Trigger:** User accepted blueprint **and** agreed to continue into research (or said “research” / “continue to research”).

**Objectives:**

1. Validate **stack choices**, APIs, hosting limits, licensing.
2. Read **existing repo** if brownfield; note constraints.
3. Use **web** when available for current docs; if offline, use **local paths + quotes** and mark gaps.

**`research.txt` rules (append only):**

- **English only**
- **Append** new blocks; **no dates required** unless user wants them
- **Organize by topic** so every URL/path is self-explanatory:

```text
Auth:
OAuth flow - https://example.com/docs/oauth
JWT refresh - ./docs/internal/auth-notes.md

Hosting:
Vercel limits - https://vercel.com/docs/limits
```

- Include **short summary** of findings per topic + **every source** used (URL or repo path)

**After research:** Update **BLUEPRINT.md §8** with a line “Research pass completed — see `research.txt` sections: …” if useful. Keep chat short; **do not** paste the entire research file unless asked.

---

## Phase 6 — Implementation plan (handoff)

**Objective:** Turn blueprint + research into **actionable next steps** without duplicating the whole blueprint.

Produce in chat (short) or a subsection at bottom of `BLUEPRINT.md` titled **“Implementation handoff”:**

1. **Immediate next 3 tasks** (ordered)
2. **Files likely touched first** (paths)
3. **Commands** to run (if known): install, build, test
4. **Blockers** still open

---

## Phase 7 — Scaffold (optional, user-controlled)

**Default:** **Describe** scaffold in the BLUEPRINT (§5). **Create** directories/empty files **only** when the user says **“create the scaffold”** or **“make the folders”** (or equivalent).

**When creating:**

- Match §5 tree; add **minimal** `README.md`, `.gitignore` if greenfield and user wants
- **No** fake secrets; **`.env.example`** with dummy keys only
- Brownfield: **minimal** additions; do not mass-delete without explicit instruction

---

## Phase 8 — Finalizing

**Before declaring planning “done”:**

1. **BLUEPRINT.md** accepted and version noted in §12
2. **research.txt** updated if Phase 5 ran
3. **§11 Definition of done** agreed with user (edit if needed)
4. **Implementation handoff** (Phase 6) visible

**After coding (outside this skill):** User or agent verifies §11; planning skill may be **re-invoked** for **v2** or **pivot** — then **revise blueprint first** (Edge cases).

---

## Edge cases (quick fixes)

1. **Skip steps / “just code”** → Mini blueprint + single confirm (Phase 4).
2. **Pivot mid-build** → Stop feature work; update **BLUEPRINT.md** first; re-accept.
3. **Unknown stack** → §3 = TBD + questions; §5 tree labeled **PROPOSED (pre-stack)**.
4. **Brownfield** → §5 = current + delta, not fantasy greenfield tree.
5. **Mono-repo** → §5 subsections per package.
6. **No web** → `research.txt` = paths + notes; mark **unverified** where needed.
7. **research.txt too long** → New append blocks start with `## TopicName`; archive old file to `research/archive-<topic>.md` if user asks.
8. **Conflicts with short-chat rules** → Long structure only in **files** and **numbered question lists**, not free-form chat essays.
9. **Secrets** → Placeholders only; never commit real keys.
10. **Non-code** → §5 = doc/section outline, not `src/`.
11. **Licensing** → In research + §9/§10, list deps and license notes with links.
12. **Multiple ideas** → Separate blueprints or pick primary goal for this thread.

---

## One-line summary

**Discover → spaced questions → `BLUEPRINT.md` (fixed 12 sections) → user approval → research → `research.txt` append → handoff → optional scaffold → finalize against definition of done.**
