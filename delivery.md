---
name: delivery
description: >-
  End-to-end delivery after planning: implement from blueprint in vertical slices,
  testing and verification, self-review, docs, dependencies, versioning and release
  for plugins, backends, and web apps. Core workflow in this file; extended
  patterns, CI matrix, failure modes, proxy/datapack/Docker notes in
  delivery-reference.md. Use when building features, shipping, tests, READMEs,
  deps upgrades, or implement / ship / polish / production-ready.
---

# Delivery — implement, verify, document, ship

Fills the gap **after** `plan` and beside `debug`, `responses`, `config-designing`. **Production-grade** execution: verified, documented, not endless refactors.

**Precedence:** No large implementation without **accepted** `BLUEPRINT.md` (escalate to `plan`). **`responses`** = chat brevity. **`debug`** = failures. **`config-designing`** = Paper GUI/message polish after correctness.

**Deeper material:** [delivery-reference.md](delivery-reference.md) — open only when the task matches (see §17).

---

## Table of contents

| § | Topic |
|---|--------|
| 0 | Trivial / tiny fixes |
| 1 | Core principles |
| 2 | Session hygiene |
| 3–9 | Phases A–G |
| 10 | Other skills |
| 11 | Anti-patterns |
| 12–15 | Stack checklists |
| 16 | Templates |
| 17 | When to open reference |

---

## Read order (default)

1. **Always skim:** §0 (skip if not trivial), §1–2, then **§3–7** for the current slice (**include §5 C.7 definition of done**).
2. **Open §8–9** when touching deps, versions, or release.
3. **Open §12–15** only if the **stack matches** (Paper vs Gradle-only vs web vs release day).
4. **Open [delivery-reference.md](delivery-reference.md)** for: failure triage lists, full logging policy, CI matrix/artifacts, Velocity/datapack/Docker, merged “depth” topics — **not** every message.

---

## 0. Trivial edits & tiny brownfield fixes

**Use this path** when the user points at **one obvious issue** (typo, single wrong import, one null check, one config key) **and** scope is clearly **not** a feature or milestone.

- **Do:** apply the minimal fix; **run the project’s normal build** (or typecheck) if the repo always builds on change.
- **Skip:** full Phase D self-review grid, Phase E doc pass, changelog — **unless** the change is user-visible or user asks.
- **Still do:** don’t introduce secrets; don’t skip build if it’s one command.

If ambiguity (“might be architectural”), **drop back** to §3–7.

---

## 1. Core principles

1. **Vertical slices** — thin end-to-end increment (data → logic → surface → verify), not all models then all UI.
2. **Blueprint-aligned** — milestones map to **BLUEPRINT.md** §7 or explicit user override.
3. **Evidence of done** — verification (tests and/or scripted smoke); no known P0 breaks.
4. **Small diffs** — reviewable chunks unless user wants bulk.
5. **No silent scope creep** — new work → blueprint update or one-line user OK.
6. **Secrets never in repo** — env samples only, redacted logs.

---

## 2. Session hygiene (same family as `debug`)

- **Todos:** ordered, one `in_progress`; steps checkable (≈1–2 files or one feature when possible).
- **Checkpoints** before: big dep upgrades, schema migrations, mass renames, whole-tree format — unless user asked for bulk.
- **Progress /100** when asked: number + **3–5 bullets** tied to todos + verification.

---

## 3. Phase A — Pre-flight

- **A.1** Open **BLUEPRINT.md**; note P0, non-goals, §5 layout, §11 definition of done. Greenfield major work without blueprint → **`plan` skill** first.
- **A.2** Smallest slice with user-visible or API-visible value; list files likely touched.
- **A.3** Risk scan: permissions, concurrency, persistence, reload (plugins), breaking consumers, migrations.

**Output:** one-line slice goal + todos.

---

## 4. Phase B — Implementation discipline

- **B.1** Match repo conventions; one responsibility per module; minimal public API.
- **B.2** Safe defaults; early config validation with clear errors; resources in correct tree (`src/main/resources` / framework equivalent); `saveDefaultConfig()` patterns where applicable.
- **B.3** Fail fast on programmer errors; graceful degrade on soft deps. Log levels: full policy in **`delivery-reference.md` §R5** (aligns with this section).
- **B.4** Bukkit/Paper: **no blocking I/O on main**; schedulers; shared state thread-safe or documented.
- **B.5** Target the API for the **pinned** server/framework version (**`paper-api` / `build.gradle` / README** — do not hardcode a game version in new code comments).
- **B.6** Hot paths: avoid re-read disk per event; cache with **invalidation** on reload. *(Caching: `delivery-reference.md` §R4.)*
- **B.7** Gate risky behavior behind config or permission.
- **B.8** Compile/typecheck after each logical unit — don’t stack 20 errors.
- **B.9** Plugin lifecycle, dynamic commands (follow repo), Adventure native — **`delivery-reference.md` §R2**.
- **B.10** Web: separate transport/domain/persistence; validate inputs; XSS-safe outputs; env-based config.
- **B.11** DB: versioned migrations; transactions; clear constraint errors.
- **B.12** Observability: structured context where helpful; match existing metrics patterns.

---

## 5. Phase C — Testing and verification

- **C.1** Strategy: library → unit; plugin commands → integration + manual smoke; web API → contract + integration; UI → e2e if harness exists else smoke script.
- **C.2** Priority: P0 paths → failure modes (bad input, perms, missing soft-dep) → reload if promised → regression for fixed bugs.
- **C.3** Deterministic tests; independent; clear names; AAA structure.
- **C.4** If tests thin: numbered smoke in chat or `docs/SMOKE.md`.
- **C.5** Gradle/Maven: `test` green if present; `build` green.
- **C.6** Node: `npm`/`pnpm test`, `npm run build` as configured.
- **C.7 Definition of done** — before “feature complete”:

  - [ ] Builds  
  - [ ] Tests updated **or** smoke documented + run  
  - [ ] P0 scenarios verified  
  - [ ] No new secrets  
  - [ ] Default log level sane  

- **C.8** Flaky tests: quarantine with ticket or fix root cause — no silent `sleep` without comment.

**Test risk catalog:** `delivery-reference.md` §R1.

---

## 6. Phase D — Self-review

Before “done,” scan: correctness vs blueprint; edge cases; server-side authz; injection/authz/logging; timeouts/retries; duplication; magic numbers; comments = why; N+1 / main-thread block; target **pinned** runtime; GUI copy + **config-designing** if applicable.

---

## 7. Phase E — Documentation

README: what/why; **requirements** (Java/Node/server versions **as stated in README or build files**); build/run; config overview; plugin commands/perms table; troubleshooting. CONTRIBUTING if multi-dev. Javadoc/TSDoc for public APIs. Upgrade/migration notes when behavior changes. CHANGELOG (Added/Changed/Fixed/Removed).

---

## 8. Phase F — Dependencies and tooling

Why + license; pin versions; avoid duplicate libs; upgrade one major at a time; read release notes; lockfiles committed; wrapper committed; CI matches local commands — extended CI matrix/artifacts: **`delivery-reference.md` §R7**. Supply chain: trusted coords, occasional audit.

---

## 9. Phase G — Versioning and release

Semver when applicable; tags/branches per repo; artifact paths (`build/libs/` JAR, web `dist`). Pre-release: version sync (`plugin.yml` expansion etc.), changelog, green build, smoke. Post-release: notes + breaking changes loud. **Docker / compose / proxy / datapacks:** **`delivery-reference.md` §R8**.

---

## 10. Integration with other skills

| Situation | Skill |
|-----------|--------|
| Chat length | `responses` |
| New big project | `plan` |
| Menus / holos / messages | `config-designing` |
| Failures | `debug` |
| Heavy API verification | user research + `research.txt` (workspace rules) |

---

## 11. Anti-patterns

Mega-PR without ask; skip build “because obvious”; TODO in prod without ticket; copy-paste files instead of shared util; silent behavior change without changelog/migration; invent commands/perms vs blueprint without OK.

---

## 12. Extended checklist — Java Paper feature

- [ ] `plugin.yml` commands/perms; softdepends  
- [ ] Listeners/tasks; cancel on disable  
- [ ] Config reload validates + errors  
- [ ] Adventure consistent; PAPI tested both ways  
- [ ] Sounds/enums valid for **pinned `paper-api`**  
- [ ] No Adventure shading  

---

## 13. Extended checklist — Gradle health

- [ ] Java toolchain / `sourceCompatibility` matches **README + `paper-api` pin**  
- [ ] Trusted repos; `compileOnly` vs `implementation` correct  
- [ ] Resources expand version into `plugin.yml`  
- [ ] Shadow excludes server-provided libs if applicable  
- [ ] `gradlew build` clean on **CI JDK matrix** (see `delivery-reference.md` §R7)  

---

## 14. Extended checklist — web API feature

Validation schema; correct HTTP codes; consistent error JSON; rate limits if public; CORS matches deploy; auth on protected routes.

---

## 15. Extended checklist — release day

Version sync across files; release notes; artifact smoke; rollback known; monitoring if ops needs.

---

## 16. Templates

**Slice completion**

```text
Slice: <name>
Blueprint: §7.M<n>
Build: PASS/FAIL
Tests: PASS/FAIL/SKIP (why)
Smoke: done / deferred (why)
Risk: …
Follow-ups: …
```

**Smoke stub (`docs/SMOKE.md`)**

```markdown
# Smoke — <project>
## Env
- JDK / server / OS (versions from README)

## Steps
1. …
2. …

## Expected
- …

## Known issues
- …
```

**PR / operator / matrix templates** — `delivery-reference.md` §R10.

---

## 17. When to open `delivery-reference.md`

| Need | Section |
|------|---------|
| Slice examples, test catalog, done/merged/released, flags, rates, drift | R1 |
| Paper threading, events, perms, PAPI, chunks, headless test | R2 |
| Web a11y, i18n, HTTP, latency | R3 |
| Cache/migration/rollback/perf/leaks/JSON | R4 |
| Logging levels, operator errors, incidents, API keys | R5 |
| Security, GDPR-ish | R6 |
| CI matrix, JDKs, artifacts, Gradle/Node/Git, packaging | R7 |
| Docker, Velocity, datapacks, staging, Windows | R8 |
| Failure mode bullets | R9 |
| Long PR/quickstart/matrix templates | R10 |
| Solo review + polish + merge gate | R11–R12 |

---

**One-line summary:** Slice vertically from blueprint; implement with repo conventions; test or smoke; self-review; docs + changelog; deps + version + verify; **[reference](delivery-reference.md)** for depth when needed.
