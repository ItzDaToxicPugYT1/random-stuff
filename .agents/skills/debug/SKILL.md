---
name: debug
description: >-
  Systematic debugging for any codebase: clarify goal and symptoms, maintain
  todos/checkpoints and progress /100, scoped sweeps (performance, logic,
  bugs/syntax, compilation), run real builds/tests, max logging when errors
  appear, minimal repro, hypothesis falsification, compact execution traces,
  module wiring checks, web/issue research for exact errors, git bisect for
  regressions, user "chill" refocus drill, verify fixes and dial logging back.
  Use when the user says debug, fix errors, investigate crashes, compile
  failures, flaky behavior, or performance/logic issues.
---

# Debug — systematic investigation and fixes

This skill defines **how** to debug. **Chat tone** still follows workspace **responses** (short, no essay) unless the user asks otherwise; **technical content** (error quotes, traces, checklists) can be structured and dense.

---

## 1. When this skill applies

Invoke for: compile/test failures, runtime errors, wrong behavior, performance regressions, flaky flows, “something broke after a change,” or when the user explicitly says **debug**, **fix**, **investigate**, **trace**, **why does it fail**.

**Do not** skip **evidence** (real logs, build output) in favor of guesses.

---

## 2. Session hygiene (high success rate)

### 2.1 Todo list

At the start of a debug session, create or update a **visible todo list** (tool todos or a markdown checklist in chat):

- Order items **smallest safe step first** (repro → narrow → fix → verify).
- **One primary in_progress** item when possible.
- Mark items **completed** as soon as done; add new items when scope grows.

### 2.2 Checkpoints

Offer **checkpoints** before irreversible or heavy steps: “If X looks wrong, stop here.” The user may say **pause**, **checkpoint**, or **confirm before …** — honor that.

### 2.3 Progress /100

When the user asks **how far** (e.g. “/100”) or **progress**:

- Give a **single number** plus **3–5 bullets** explaining *why* (what is proven, what is unknown, what is blocked).
- Avoid vague “almost done” without criteria tied to the todo list.

### 2.4 Smaller steps

Prefer **micro-steps** with verification between them: smaller steps → **higher success rate**, easier rollback, clearer causality.

---

## 3. User refocus drill (“chill” protocol)

If the assistant is **over-generating** or drifting, and the user signals **chill**, **stop the essay**, **5 words**, or similar:

1. **Next reply:** obey **~5 words** if they asked for that; otherwise **one line**.
2. On **“What is the exact problem — don’t say anything else”:** reply with **only** the precise problem statement (one sentence max).
3. On **“What is the fix”:** propose **the minimal fix** (short); no extra preamble.
4. On **“Name 3 other difficulties”:** list **exactly three** risks, blockers, or edge cases — **one line each**.

Then resume normal workflow with todos and evidence.

---

## 4. Phase A — Clarify the end goal

Answer internally (and briefly in chat):

- **Expected behavior** vs **actual behavior**.
- **User-visible symptom** (error text, screenshot description, “command X does Y”).
- **Scope:** single feature, whole project, production vs dev.

If the user has **no** error output yet, the first todo is **obtain evidence** (build log, stack trace, server log snippet).

---

## 5. Phase B — Evidence and max observability

### 5.1 Compiler and console

When errors come from **compilation** or **runtime console**:

- **Crank observability up** for the **smallest subsystem** that still reproduces: verbose/trace logging, exception full stacks, Gradle `--stacktrace` / `--info` when appropriate, etc.
- **Capture exact text** — quote errors **verbatim** (file, line, message). **Do not paraphrase** stack traces for decisions.

### 5.2 Guardrails

- **No secrets** in logs (tokens, passwords, private keys). Use redacted placeholders.
- After the fix is verified, **turn debug verbosity down**; remove temporary spam logs unless the user wants them for ongoing diagnosis.

### 5.3 Environment matrix

Check **versions** that commonly cause false “bugs”:

- **Java/JDK**, build tool (**Gradle/Maven**), **Node/npm**, runtime (**Paper/Spigot** version, etc.).
- **OS-specific** issues (paths, PowerShell vs bash, line endings).

Document mismatches in the todo notes or a short “Environment” bullet.

---

## 6. Phase C — Minimal reproduction

Before large refactors:

- **Smallest input** / **smallest command** / **shortest path** that fails **reliably**.
- If reproduction is intermittent, note **frequency** and **conditions** (timing, concurrency, reload).

**No fix is “done”** without reproducing post-fix behavior (or explaining why repro is impossible and what was verified instead).

---

## 7. Phase D — Hypotheses and falsification

1. List **3–5 hypotheses** (short bullets): what could cause the symptom.
2. For each, define **one cheap falsification test** (run one test, grep one symbol, read one frame of stack).
3. **Drop** hypotheses contradicted by evidence; **don’t** pile fixes for dead theories.

This pairs with the user’s **“3 other difficulties”** prompt for **risk surfacing**.

---

## 8. Phase E — Scoped project sweep

**Avoid** “read the entire repo” on large projects unless necessary.

**Default scope:** entry points, files touched recently, packages mentioned in stack traces, config loaders, lifecycle (`onEnable`, `main`, routers).

Perform a structured sweep; record findings as bullets:

### E.1 Performance

- Hot loops, unnecessary allocations, **O(n²)** patterns, blocking calls on critical threads, missing caching where obvious, N+1-style patterns (DB or event handlers).

### E.2 Logic

- Wrong conditionals, inverted flags, off-by-one, race conditions, **reload** vs **cold start** differences, stale state.

### E.3 Bugs (correctness & API use)

- **Mismatching** variables, methods, types, imports, renamed symbols.
- **Syntax** issues, wrong API for the target version, null/empty handling, wrong event priority, broken placeholders.

### E.4 Compilation and static risk

- Imports, generics, visibility, missing deps, **deprecated** API use.
- **Run the real build** (`./gradlew build`, `mvn`, `npm run build`, `tsc`, etc.) and treat **tool output** as authoritative.

---

## 9. Phase F — Five complex test cases (interactive / event-driven systems)

For **interactive** projects (games, plugins, UIs, servers):

1. Invent **five** **non-trivial** scenarios (edge permissions, empty input, reload, concurrent users, invalid config, etc.) — not five copies of the happy path.
2. For **each** scenario, produce a **compact execution trace**, **not** a wall of “every line in the repo.”

**Trace format (mandatory shape):**

```text
Case: <name>
Trigger: <user action or event>
Chain: ClassA.method → ClassB.method → … → <outcome or throw>
Branches: File.java:line — why this branch
```

Include **file:line** at **branch / handoff** points only. If uncertain, **mark uncertain** and add a falsification step (logpoint, breakpoint, extra log).

---

## 10. Phase G — Module inventory and wiring

1. **List modules** (simple flat list): e.g. `ConfigManager`, `Main plugin class`, `Command X`, `Service Y`, `Database layer`, `Scheduler`, etc.
2. For **each**, verify in code:
   - **Initialized** in the correct order (dependencies before dependents).
   - **Wired** into callers (no dead registration, no missing `register`, no listener never attached).
   - **Config / lifecycle**: reload paths, `onDisable` cleanup if relevant.

Flag **orphans**, **double init**, and **circular init** risks.

---

## 11. Phase H — Research (external truth)

When stuck or when versions/docs matter:

- Search for **exact error substrings** (quoted), official docs, migration guides, GitHub issues.
- Prefer **primary sources** over random blogs when possible.

**Optional:** append findings to **`research.txt`** (project root) in **English**, **categorized**, with URLs/paths — especially if the fix depended on an external doc. Align with workspace rules for when `research.txt` is required.

---

## 12. Phase I — Regression isolation

If it **used to work**:

- **Identify last known good** commit or version.
- Use **`git bisect`** or narrow the diff window when feasible.
- Correlate with **dependency** or **API** upgrades.

---

## 13. Phase J — Fix discipline

- **Smallest change** that addresses the **proven** cause.
- After each fix: **re-run** the same build/test/repro path.
- If two failures remain, **tackle one at a time** with ordering in the todo list.

---

## 14. Phase K — Verify and clean up

- **Confirm** the symptom is gone under the original repro.
- **Run** broader smoke checks when cheap (related commands, reload path).
- **Remove or downgrade** temporary debug noise; ensure no **secrets** left in debug prints.
- Optionally leave **one comment** or **log line** at **INFO** if ongoing diagnosis is needed (user’s choice).

---

## 15. Outputs the user may request

| Request | Response shape |
|--------|----------------|
| Status | Todo list + next 1–3 actions |
| /100 | Number + 3–5 justification bullets |
| Checkpoint | Yes/no gate + what happens next |
| Exact problem only | One sentence, nothing else |
| Fix only | Minimal fix description or patch |
| 3 difficulties | Three one-line risks |

---

## 16. Integration with other workspace skills

- **responses:** keep **chat** short; put long traces in **files** or **collapsible structure** only if the user asked for detail.
- **plan / blueprint:** if debugging reveals **wrong architecture** or **missing spec**, suggest updating **`BLUEPRINT.md`** after the fire is out.
- **config-designing:** cosmetic YAML passes **do not** override correctness fixes; fix **bugs and schema** first.

---

## 17. Anti-patterns (reject)

1. **Fixing without repro** or without seeing **real** error text.
2. **Large refactors** while the failure mode is still unclear.
3. **Paraphrased** stack traces as the basis for edits.
4. **Shotgun changes** across many files without verification between steps.
5. **Leaving** verbose debug / PII in **committed** code.

---

## 18. Final checklist (before “done”)

- [ ] End goal and symptom stated.
- [ ] Evidence captured (build/log).
- [ ] Minimal repro documented.
- [ ] Hypotheses tested / falsified.
- [ ] Sweep scoped appropriately.
- [ ] Toolchain build run after fix.
- [ ] Five traces **or** explicit reason omitted (non-interactive CLI-only project).
- [ ] Module list + wiring checked **or** scoped out with reason.
- [ ] Debug verbosity reduced; secrets not logged.
- [ ] User todos updated and completed states accurate.

---

## 19. One-line summary

**Todos + checkpoints + /100; evidence and max debug on failure; minimal repro; hypotheses falsified; scoped sweep; real build; five compact traces for interactive systems; module wiring; research if stuck; bisect if regression; small fixes with verification; clean up logging after.**
