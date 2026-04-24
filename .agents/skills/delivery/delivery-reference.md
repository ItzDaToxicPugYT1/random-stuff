# Delivery — reference (depth, appendices, commands)

**Use with:** `.cursor/skills/delivery/SKILL.md`  
**Open when:** stack-specific checklist, failure triage, CI setup, ops, or extended patterns — not for every task.

---

## R1. Workflow depth

- **Vertical slices:** plugin — bad = all DTOs then commands last; good = `/hello` + perm + config message → build → smoke → next slice adds DB. Web — good = `GET /health` + DB ping + README deploy blurb → then features. *(See SKILL §4 core narrative.)*
- **Test risk catalog** (map to real features, no empty boilerplate): auth denied (unauth + wrong role); happy minimal payload; each field invalid once; idempotency if required; concurrency consistency; resource cleanup.
- **Feature traceability table** (blueprint/wiki): Feature | Code entry | Tests | Docs § | Owner.
- **Done vs merged vs released:** done = verified + todos + user-visible docs; merged = git/PR; released = tag + artifact + notes — don’t claim released without user OK.
- **Feature flags in config:** default risky features `false`; log once when enabled.
- **Rate limits / cooldowns:** match blueprint; tell the user when limited.
- **Blueprint drift:** stop and propose blueprint update — don’t silently diverge.

---

## R2. Plugin runtime (Paper / Bukkit)

- **API version:** target whatever **`paper-api` / `build.gradle` / README** pin — never hardcode a game version string in prose here.
- **Lifecycle:** `onEnable` register; `onDisable` cancel tasks, close resources.
- **Scheduling:** scheduler APIs; cancel on disable; avoid fixed-rate pile-up under lag (catch-up policy).
- **Events:** justify `EventPriority`; MONITOR doesn’t modify; unregister dynamic listeners when short-lived.
- **Permissions / commands:** least-privilege defaults; namespace nodes with plugin name; document in README; accurate usage + tab-complete; test default / op / explicit grant; avoid command name collisions across plugins.
- **Dynamic commands:** follow repo pattern (`CommandMap` / brigadier); don’t invent without blueprint OK.
- **Adventure / MiniMessage:** native Paper API; no unnecessary shading.
- **Soft deps:** e.g. PlaceholderAPI — test with and without jar.
- **Player / UUID:** validate online state; don’t block main thread on async UUID fetch.
- **Chunks / regions:** respect threading rules for the pinned Paper version; don’t assume chunk loaded.
- **Sounds / enums:** must match **Bukkit API for pinned `paper-api`**.
- **Headless testing:** mock where feasible; else integration on test server.

---

## R3. Web & HTTP

- **A11y:** keyboard nav, labels, contrast, focus states.
- **i18n:** keys for user-visible strings; document placeholders.
- **Network:** HTTP timeouts; retry only idempotent ops; circuit breaker when infra supports.
- **Latency budget:** state max acceptable time on hot paths; profile before micro-optimizing.

---

## R4. Reliability, data & config

- **Caching:** invalidate on config reload / data change; TTL documented; thread-safe. *(Aligns SKILL §4 B.6.)*
- **Serialization / persisted formats:** version formats; forward-compatible reads when possible.
- **Config migration:** read old key if new missing → write new → log once → remove old in next major + changelog.
- **Rollback:** DB backup before risky migration; keep previous artifact; document impossible downgrades before merge.
- **Performance validation:** baseline TPS/latency on hot path; compare before/after same scenario; document accepted regression.
- **Resource leaks:** try-with-resources; close ResultSet/Statement/HTTP response; return connections to pool.
- **JSON/YAML:** validate structure; friendly parse errors; schema/records if project uses them.

---

## R5. Logging & operators

- **Levels:** ERROR = user impact / data loss; WARN = recoverable oddity; INFO = lifecycle; DEBUG = noisy (off by default prod). **Never** passwords, tokens, session cookies. *(Tightens SKILL §4 B.3.)*
- **Player-facing errors:** actionable (“check key X, perm Y, dep Z”); full stack server-side only.
- **Incident readiness:** how to enable debug fast; where logs live; what to attach to bug reports.
- **Third-party keys:** never commit; validate at startup with clear message; document how operators obtain them.

---

## R6. Security & compliance

- Injection surfaces (commands, SQL, templates); authz on sensitive actions; sensitive data not logged.
- If storing user data: document retention / deletion (GDPR-ish).

---

## R7. Build, CI, packaging, Git

- **Reproducibility:** JDK vendor note if needed; Gradle wrapper committed; no random repo resolution.
- **CI (extend SKILL §8 F.4):** workflow commands match README; **fail build on test failure** (no `continue-on-error` without reason); **matrix = project-supported JDKs** (from README/toolchain); **upload artifact** = JAR and/or web `dist` as applicable; cache deps sanely.
- **Dependencies:** `dependencyInsight` / tree; align versions; document exclusions.
- **Packaging:** fat JAR only when intended; don’t bundle server API; Proguard only if project already uses.
- **Gradle:** `./gradlew tasks | build | clean build | dependencies | dependencyInsight --dependency <name>` — Windows: `gradlew.bat`. `build -x test` only if user explicitly wants skip + document why.
- **Node:** `npm ci`, `npm test`, `npm run build`, lint/typecheck; pin repeated `npx` in devDependencies.
- **Git:** small commits; don’t commit `build/`, `.class`, `node_modules`, env files; branch/tag per team norms.

---

## R8. Ops, environments, ship extras

- **Staging vs prod:** separate configs documented; feature flags for experimental.
- **Local dev:** fast loop documented (e.g. build + copy JAR script); sample dev configs.
- **Docker (one-liner pattern):** if project ships a container, document `docker build` / `docker compose up` entrypoint + env file — match repo reality.
- **Velocity / proxy (optional):** if backend sits behind proxy, note forwarding IP / channel constraints in README or blueprint; don’t assume vanilla direct connection.
- **Datapacks / resource packs (optional):** if repo ships them: validate `pack.mcmeta`, folder structure, reload vs restart behavior, version range in README.
- **Binary assets:** optimize large blobs; document tradeoffs.
- **Windows vs Unix:** path APIs not string hacks; document PowerShell vs bash for scripts.

---

## R9. Failure modes (route to `debug` for deep dive)

- ClassNotFound / NoClassDefFound after dep add → scope, shading, version skew.
- AbstractMethodError / IncompatibleClassChangeError → clean rebuild, align API binary version.
- IllegalStateException async → main-thread violation.
- YAML reload parse errors → indentation; log file+line if available.
- Commands missing → `plugin.yml` vs dynamic registration; `knownCommands` on unregister.
- Raw placeholders → PAPI missing or parse order wrong.
- Silent feature off → permission default, config flag, soft-dep.
- IDE works, JAR fails → resources not processed; wrong `plugin.yml` main; shadow excludes.
- Flaky HTTP in plugin → timeouts, main-thread block, DNS.
- Memory creep → uncancelled tasks, unbounded caches, heavy per-event alloc → profile.

---

## R10. Templates

**PR description**

```markdown
## What
-
## Why
-
## How tested
- [ ] Build
- [ ] Tests
- [ ] Manual smoke
## Risk / rollback
-
## Checklist
- [ ] Docs
- [ ] Changelog
- [ ] Version bump (if release)
```

**Operator quickstart (fill versions from README / build files)**

```markdown
## Quickstart
1. Install Java (see README) and server (Paper version pinned in README / build)
2. Place jar in `plugins/` (or as documented)
3. First start; edit config under plugin data folder
4. Reload or restart per docs
5. Troubleshooting: …
```

**Compatibility matrix stub**

| Component        | Version source        | Notes           |
|-----------------|------------------------|-----------------|
| Paper API       | `build.gradle`         | compileOnly pin |
| Java            | README / toolchain     |                 |
| Optional plugins| README softdepend list |                 |

---

## R11. Solo review & polish

- Reviewer hat: why this abstraction? what breaks if load doubles? where tests?
- Before milestone: formatter/linter; dead imports/code; todos completed; `git status` intentional.

---

## R12. Naming, flags, antipatterns

- **Config keys:** stable; migrations if renamed.
- **Never** `Thread.sleep` on Bukkit main thread; use scheduler.
- **Final merge gate (solo):** build green; README matches commands/config; no scratch files.
