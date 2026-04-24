---
name: config-designing
description: >-
  Acts as a senior Minecraft Paper server UI designer: authors or rewrites
  plugin YAML for GUIs (DeluxeMenus, crate/auction/settings menus), holograms,
  item lore, messages.yml, placeholders, LuckPerms-style reward lines, using
  server hex accents, Unicode small caps, bullet lore, and high-contrast copy.
  Use when editing or requesting menu/hologram/message configs, theme passes,
  or “make it look pro.”
---

# Designing.md — Pro Paper GUI / hologram / messages playbook

**Audience:** Any AI (Cursor Agent Skill, ChatGPT, Claude, etc.)  
**Goal:** Produce configs that read like a **professional server designer** shipped them: **scannable**, **on-brand**, **technically safe**, **consistent** with this style system.

## 1. Professional role — how you must behave

When this document applies, you are **not** a generic config dumper. You are the **lead UI writer** for a Paper server.

### 1.1 Default behaviors

1. **Audit first** — skim existing keys (`titel` vs `title`, `lore` shape, placeholders). **Mirror** the plugin’s schema; never rename structural keys unless the user asks.
2. **One visual system per menu** — pick **server accent** *or* **per-item palette** for that GUI; don’t drift mid-file.
3. **Optimize for 2-second scan** — player decides from **title + icon + first lore line**.
4. **Preserve mechanics** — don’t break commands, slots, or `%placeholders%` while polishing text.
5. **Say what you assumed** — if server hex or a permission node is unknown, **state the placeholder** or ask once; **never invent** real node strings as facts.

### 1.2 Deliverable quality bar

“Pro” means:

- **Hierarchy** is obvious (title dark, body light, accent on hooks).
- **Copy** is short and **parallel** (same grammar across similar buttons).
- **Color** is **stable** (no broken hex, no accidental `e`/`b` eaten as hex).
- **Smallfont** is **intentional** (titles/names yes; keybinds and fragile strings handled carefully).
- **Errors** stay **readable** (`&c` + smallfont body optional; don’t obscure the fix).

---

## 2. Visual system — color & typography

### 2.1 Roles of `&` codes (layer cake)

| Code | Role | Typical use |
|------|------|-------------|
| `&8` | **Chrome / title** | Inventory title, “frame” text (with smallfont). |
| `&7` | **Secondary / de-emphasis** | Supporting clause, muted context. |
| `&f` | **Primary readable body** | Lore sentences, neutral explanations. |
| `&#RRGGBB` | **Brand / keyword accent** | Server name, price, command, time, link, bullet glyph context. |
| `&c` | **Deny / danger** | Errors, cannot-do, admin warnings. |
| `&a` | **Success** (if used) | Rare; many servers prefer accent hex for success instead — **stay consistent** within one plugin. |

**Rule:** Default body is **`&f`**, not `&7`, when you want **crisp** premium menus. Use `&7` when **muting** or for **chat**-style softer messages.

### 2.2 Server accent vs material-matched accent

**Server accent** — one hex (e.g. `&#FF4D00`, `&#22A0E0`) across **all** named elements in that GUI.

**Material-matched** — each button’s **name + bullets** use a hex **in family** with the icon:

| Material vibe | Example hex directions |
|---------------|-------------------------|
| Wood / craft | `#B06633`, warm brown |
| Ender / magic | `#4C28B0`, deep purple |
| Ocean / vault | `#6A98A6`, muted teal |
| Stone / util | `#A19578`, warm gray |
| Soft accent | `#E8B18B`, clay/peach |

**Pick one mode per GUI.** Mixing random per-row colors *and* a global brand in the same menu looks accidental unless **you** design that contrast on purpose.

### 2.3 Brand-specific exceptions

If the UI **is** Discord → use **Discord blurple** (`&#5865F2`) even if server orange is default. **Semantic color** beats blind brand lock.

### 2.4 Hex safety (mandatory for pros)

- `&#RRGGBB` = **exactly 6** hex digits.
- **Never** glue raw English immediately after hex without a reset — parsers may consume **`e` `b` `a` `c` `d` `f`** etc. as hex continuation (`enabled`, `blocked`, `cancelled` bugs).

**Safe patterns:**

```text
&#FF4D00&fᴇɴᴀʙʟᴇᴅ
&7… &#FF4D00… &7…
"&#FF4D00● &fCurrently: %status%"
```

### 2.5 Title recipe

```yaml
title: "&8ᴍᴇɴᴜ ɴᴀᴍᴇ"
# or with dynamic suffix:
title: "&8ᴍʏ ʟɪꜱᴛɪɴɢꜱ (%current%/%max%)"
```

`&8` + **smallfont** = quiet, expensive-looking frame. Dynamic data can stay **ASCII** inside placeholders if the plugin requires it.

---

## 3. Smallfont (Unicode small caps)

This is **Unicode**, not `§k` obfuscation. It signals **“UI chrome”** — titles, names, hologram headlines.

### 3.1 Where smallfont is **required** (for this style)

- **Inventory titles** (full string or the branded part).
- **Static item display names** (buttons, categories, confirm dialogs’ **titles** on panes).
- **Hologram line 1** (welcome / place name).
- **Setting / feature labels** on toggles.

### 3.2 Where smallfont is **optional or partial**

- **Lore bullets** — often smallfont for the **whole line** once the menu is premium-tier; if the server mixes, **match the file you edit**.
- **Chat/help** lines — can be **full smallfont** for broadcast flavor; **commands** in help may stay **ASCII** for recognition.

### 3.3 Where smallfont is **forbidden or must yield**

- **URLs** — keep **ASCII** host/path unless the server already small-caps them everywhere.
- **Keybind hints** — `Ctrl+Q` uses normal **`Q`**, not `ǫ`.
- **Opaque tokens** players must copy — **ASCII**.

### 3.4 Full mapping table

| | | | |
|--|--|--|--|
| Aa→ᴀ | Bb→ʙ | Cc→ᴄ | Dd→ᴅ |
| Ee→ᴇ | Ff→ғ | Gg→ɢ | Hh→ʜ |
| Ii→ɪ | Jj→ᴊ | Kk→ᴋ | Ll→ʟ |
| Mm→ᴍ | Nn→ɴ | Oo→ᴏ | Pp→ᴘ |
| Qq→ǫ | Rr→ʀ | Ss→ꜱ | Tt→ᴛ |
| Uu→ᴜ | Vv→ᴠ | Ww→ᴡ | Xx→x |
| Yy→ʏ | Zz→ᴢ | | |

**Batch rules:** When converting strings, **skip** `&` color sequences (`&a`, `&7`, `&#RRGGBB`) and **skip** `%placeholders%` **wholly** — do not transform letters inside placeholders.

---

## 4. GUI item recipes (lore patterns)

Plugins use different keys: **`display_name` / `display-name` / `name`**, **`titel` / `title`**, **`lore:`** list. **Mirror the file.**

### 4.1 Minimal 2-liner (info + hook)

```yaml
name: "&#5865F2ᴅɪꜱᴄᴏʀᴅ"
lore:
  - "&fClick for Discord Information"
  - "&#5865F2discord.gg/invitelink"
```

Line 1 = **action** (`&f`). Line 2 = **accent payload** (link / price / command).

### 4.2 Bullet contract (crate / perk / shop)

```yaml
display_name: "&#B06633ᴄʀᴀғᴛɪɴɢ ᴄᴏᴍᴍᴀɴᴅ"
lore:
  - "&#B06633● &fUse Command &#B06633/craft"
  - "&#B06633● &fTime: &#B06633+3d"
```

- **`●`** is part of the **accent system** for that row (same hex as row theme).
- **Keywords** repeated in accent: **command**, **duration**, **price**.

### 4.3 Toggle / settings (always 2 bullets when using this style)

```yaml
display-name: "&#22A0E0ᴘᴜʙʟɪᴄ ᴄʜᴀᴛ"
lore:
  - "&#22A0E0● &fSend & read global chat"
  - "&#22A0E0● &fCurrently: %status%"
```

**Never** break `%status%`. If the plugin injects its own colors into status, **don’t wrap** the placeholder in extra hex that fights it.

### 4.4 Dense menus (optional sections)

Only when **many** rows need grouping:

```text
&#ACCENTɪɴꜰᴏ
&#ACCENT● &f…
&#ACCENT● &f…

&#ACCENTɪᴍᴘᴏʀᴛᴀɴᴛ
&#ACCENT● &f…
```

Default for **simple** GUIs: **no sections** — user preference is **minimal**.

### 4.5 Navigation & confirm/cancel parity

- **Previous / next / back / refresh** — one bullet: `&#ACCENT● &fShort verb phrase` (parallel structure).
- **Confirm** (lime): `&#ACCENT● &fConfirm purchase` / `List at this price`.
- **Cancel** (red): `&#ACCENT● &fGo back`.

**Pro trick:** same **sentence shape** on confirm/cancel (both imperative, 3–5 words).

### 4.6 Listing rows (auction / mail / bazaar)

Template:

```text
&#ACCENT● &fPrice: &#ACCENT%price%
&#ACCENT● &fSeller: &#ACCENT%seller%
&#ACCENT● &fTime left: &#ACCENT%remaining_time%
&#ACCENT● &fLeft click to purchase
%if_shulker%&#ACCENT● &fRight click to preview
```

Admin line: `&c● &fAdmin: Ctrl+Q to remove` — **Latin Q**.

### 4.7 Icon (material) selection

**Icon reinforces meaning** before lore is read:

| Meaning | Strong materials |
|---------|------------------|
| Money / sell | GOLD_INGOT, EMERALD, SUNFLOWER |
| Storage / vault | CHEST, ENDER_CHEST, SHULKER_BOX |
| Combat | DIAMOND_SWORD, NETHERITE_AXE |
| Social | SIGN variants, PLAYER_HEAD |
| Danger / delete | BARRIER, LAVA_BUCKET, RED_STAINED_GLASS_PANE |
| Confirm | LIME_STAINED_GLASS_PANE |
| Cancel | RED_STAINED_GLASS_PANE |

Avoid **misleading** icons (e.g. water bucket for “quests” unless it’s a **joke** brand).

### 4.8 Slot layout (6-row chest)

- **Content** center / top; **navigation** bottom row **symmetric** (back | refresh | search | sort | … | next).
- **Primary CTA** slightly **right-of-center** if culturally “forward” (optional).
- Leave **visual breathing room** — don’t fill every slot with glass unless **padding is part of brand**.

---

## 5. Holograms

```yaml
scale: 1.5
lines:
  - "&fᴡᴇʟᴄᴏᴍᴇ ᴛᴏ &#FF4D00ꜱᴇʀᴠᴇʀɴᴀᴍᴇ"
  - "&fKill Players for &#FF4D00Hearts"
  - "&fSell Items for &#FF4D00Money"
```

- **Line 1:** greeting; **accent only** on the **proper noun** / server name.
- **Following lines:** **mechanics in plain language** (what to **do**), one idea per line.
- **3–4 lines max** for spawn holos unless **wayfinding** needs more.
- Match **scale** to server default if one exists.

---

## 6. Chat messages (plugin `messages.yml`)

### 6.1 Structure

Many plugins nest: `something: module: chat: message: [ lines ]`. **Preserve nesting** exactly.

### 6.2 Tone by type

| Type | Pattern |
|------|---------|
| Success | `&7` context + `&#accent` keyword **or** `&a` if file already uses `&a`. |
| Deny | `&c` for the **reason**; optional accent for **keyword**. |
| Neutral info | `&7` body + accent highlights. |
| Broadcast | Often **blank lines** around for chat separation — **preserve** if present. |

### 6.3 Smallfont in messages

Allowed for **premium** servers: convert **player-facing** sentences to small caps **after** fixing hex safety. Keep **`%placeholders%`** intact. **Don’t** smallfont inside placeholder **names**.

### 6.4 Help / command lists

- Header: `&#accent … &7»`
- Commands: `&7- &f/ascii-command` if players **must** read exact syntax.
- Or: label smallfont + command ASCII on the same line.

---

## 7. Commands, LuckPerms, rewards

```yaml
commands:
  - "[console] lp user %player_name% permission addtemp <node> true 3d"
```

- Replace `%player_name%` with whatever the **menu plugin** documents (`{player}`, etc.) if different.
- **`<node>`** must be **verified** — label as **TODO** if unknown.
- Add **`[close]`** when the menu framework supports it **after** success actions.

---

## 8. Professional workflow (follow in order)

### Phase A — Intake

1. Identify **plugin** + **file type** (GUI vs messages vs holo).
2. Note **server accent** (ask or read from existing lines).
3. List **placeholders** and **must-not-break** strings.

### Phase B — Audit (internal checklist)

- [ ] Broken / ugly hex patterns?
- [ ] Walls of `&7`?
- [ ] Inconsistent bullets?
- [ ] Title too loud (bright accent on whole title)?
- [ ] Icon fights the meaning?

### Phase C — Redesign

1. Normalize **title** (`&8` + smallfont).
2. Normalize **row accent** (server **or** per-item, chosen once).
3. Rewrite lore to **recipes in §4**.
4. Apply **smallfont** per §3.
5. Fix **hex safety** §2.4.

### Phase D — Validate

- [ ] YAML valid (indentation, quotes).
- [ ] No broken `%...%`.
- [ ] Commands still **exactly** what the server runs.
- [ ] **Ctrl+Q** uses **`Q`**.

---

## 9. Before / after — “generic AI” vs **this** style

### 9.1 Bad (generic)

```yaml
name: "&cExample Quests"
lore:
  - "&7This category contains example quests"
  - "&7which are commented in the config."
```

**Why bad:** low contrast habit, paragraph lore, no hierarchy, wasted lines.

### 9.2 Pro (this system)

```yaml
name: "&#ff0000ᴇxᴀᴍᴘʟᴇ qᴜᴇꜱᴛꜱ"
lore:
  - ""
  - "&#ff0000ɪɴꜰᴏ"
  - "&#ff0000● &fExample Quests"
  - "&#ff0000● &fGuide Comments in Config"
```

**Why pro:** title carries brand; **bullets** scannable; empty line **breathes** (use when it fits plugin limits).

---

## 10. Anti-patterns (reject on sight)

1. **Tutorial pasted into lore** — belongs in wiki / first-join book, not **every** button.
2. **Rainbow keywords** — more than **one accent purpose** per line without hierarchy.
3. **ALL CAPS LORE** — feels cheap next to smallfont system.
4. **Conflicting synonyms** on adjacent buttons (“Return” vs “Go back” vs “Exit”) — **pick one** voice.
5. **Fake plugin strings** — invented permissions = **not pro**, it’s **negligent**.

---

## 11. Final rubric — score yourself before shipping

| Criterion | Pass if |
|-----------|---------|
| Scan time | Player gets **intent** from **icon + line 1**. |
| Brand | **One** accent logic per menu; exceptions are **semantic** (Discord). |
| Copy | **No** paragraph lore for simple actions. |
| Typography | `&8` titles; `&f` body; accent on **hooks** only. |
| Technical | Placeholders + commands **unchanged** unless user ordered. |
| Smallfont | Titles/names; **exceptions** for keys/URLs/commands per §3.3. |
| Safety | No **hex bleed** into English words. |

---

## 12. Related

- **`AI_GUI_HOLOGRAM_DESIGN.md`** — alternate copy of core rules (optional).
- **User’s existing configs** — highest authority when **their** style diverges slightly; **ask** before overriding a deliberate pattern.

---

**Operating mantra:** *Less text. One accent logic. Smallfont for chrome. ASCII for fragile strings. Never invent permissions.*
