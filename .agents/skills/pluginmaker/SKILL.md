---
name: pluginmaker
description: >
  Minecraft Paper plugin development skill for Java 21 and Paper 1.21.x. Use for: plugin coding,
  Paper/Bukkit/Spigot API, event handling, Brigadier commands, Adventure/MiniMessage, Data Component
  API, PDC, entity/block/item manipulation, inventory GUIs, scheduler/async tasks, packet handling,
  NMS access, paperweight-userdev, plugin.yml/paper-plugin.yml, Gradle/Maven setup,
  ProtocolLib/PacketEvents, world generation, scoreboard/boss bar/title/tab list, resource packs,
  database, config, permissions, PlaceholderAPI. Trigger on: plugin, addon, Paper, Bukkit, Spigot,
  Minecraft, server mod, SMP, or any Java+Minecraft development context.
---

# 🔧 The Ultimate AI Plugin Development Rulebook

> \*\*Version:\*\* 2.0 — Battle-tested on the Broadcast Plugin (Paper 1.21.1)
> \*\*Author:\*\* ToxicPug × AI Pair Programming
> \*\*Last Updated:\*\* March 27, 2026

This document is the **complete, detailed rulebook** for building Minecraft Paper plugins with AI assistance. It captures every lesson, every pitfall, every rule, and every step — so any AI that reads this can replicate the process perfectly, first try.

\---

## 📜 Communication Rules (READ FIRST)

These rules override everything else. If the AI breaks these rules, the user WILL be annoyed.

1. **Short replies** — use single words when possible. No essays.
2. **One-sentence questions** = one-word/short answers.
3. **Explanations** = examples, bullets, code blocks — NEVER paragraphs.
4. **Lists** = bullet points or numbered, each item on its **own line** with spacing between groups.
5. **Chill vibes** — no formal/victorian language. Talk like a dev, not a professor.
6. **Compile/runtime errors** = **STOP immediately**, ask user before continuing.
7. **Never group questions into one paragraph** — always number them with line breaks between each.
8. **When the user says "LGTM"** or gives a thumbs up → move forward immediately, don't ask again.
9. **When the user says something is wrong** → fix it, don't argue or explain why it was "correct."

\---

## 🏗️ The 8-Phase Process (Strict Order)

### Phase 1: Discovery

> AI asks user for a brief description of the plugin.

* What does it do?
* What features?
* Any specifications? (namespace, server version, etc.)

This is just a jumpstart — not the final idea. Keep it casual.

**OUTPUT:** A 2-3 sentence summary of the plugin idea.

\---

### Phase 2: Deep Dive Questions

> AI asks \*\*10 questions\*\* — 5 specific + 5 yes/no. One question per line, with spacing.

**Format the questions like this (WITH SPACING):**

```
1. What permission should players need to use /broadcast?

2. Should admins have a separate permission for reloading?

3. Do you want sounds to play when a broadcast is sent?

4. Should it support PlaceholderAPI?

5. Do you want a cooldown system?
```

**NEVER do this:**

```
1. What permission? 2. Should admins? 3. Do you want sounds? 4. PlaceholderAPI? 5. Cooldown?
```

The user hates questions grouped into a single paragraph. Always space them out.

**Examples of good specific questions:**

* "What should happen when a player does X?"
* "Should admins have a separate GUI?"
* "What sound effect should play?" (give examples like `ENTITY\_PLAYER\_LEVELUP`)

**Examples of good yes/no questions:**

* "Should it support PlaceholderAPI?"
* "Do you want a MySQL option or is SQLite fine?"
* "Should console be able to run the commands too?"

**OUTPUT:** User's answers, which define the full feature set.

\---

### Phase 3: Blueprint Generation

> AI generates a detailed \*\*blueprint.md\*\* file in the project root (NOT inside the compile folder).

The blueprint MUST contain these sections in this exact order:

#### Section 1: General Description

* Plugin name, version, target software, Java version, namespace

#### Section 2: All Commands \& Permissions

* Full table with Command | Permission | Description

#### Section 3: Player \& Admin Scenarios

* Real-world use-case walkthroughs
* "Admin opens config, edits X, runs reload, Y happens"
* "Player types /command, sees title, hears sound"

#### Section 4: Feature List \& Research Links

* Every feature explained
* Research links to relevant API docs:

  * Paper Docs: https://docs.papermc.io/paper/dev
  * Adventure Components: https://docs.papermc.io/adventure/introduction
  * MiniMessage Format: https://docs.papermc.io/adventure/minimessage/format
  * PlaceholderAPI Wiki: https://wiki.placeholderapi.com/developers/using-placeholderapi/
  * Bukkit Sound Enum: https://hub.spigotmc.org/javadocs/bukkit/org/bukkit/Sound.html

#### Section 5: Technical Specifications

* Every Java file name + its purpose
* Complete folder layout (tree format)
* Full preview of every config file (config.yml, messages.yml, sounds.yml, etc.)

#### Section 6: Implementation Order

* What gets built first, second, third, etc.

**CRITICAL:** The blueprint goes in the **working directory root**, NOT inside the compilation folder.
Example: `C:\\Server dev-ing\\Test Plugin broadcast\\blueprint.md` — NOT inside `Broadcast/`.

**OUTPUT:** `blueprint.md` in project root.

\---

### Phase 4: Blueprint Review

> User reviews the blueprint. \*\*WAIT FOR CONFIRMATION.\*\*

⚠️ **DO NOT START CODING UNTIL THE USER SAYS IT'S GOOD.**

#### ✅ If User Accepts

* Move to Phase 5.

#### ❌ If User Says Something Is Missing

* Ask **10 new questions** to finalize details.
* Regenerate the blueprint with changes.
* Loop back to review.
* Keep looping until user is satisfied.

#### 🔄 If User Wants Changes to Format

* Rewrite the blueprint in the format they want.
* Don't argue about format — just do it their way.

\---

### Phase 5: Research \& Planning

> After user accepts the blueprint, do research BEFORE writing any code.

1. **Research** — search online for:

   * Related APIs (Vault, PlaceholderAPI, TAB, etc.)
   * Similar plugins for inspiration
   * Paper API docs for relevant methods
   * Adventure API for text component handling
   * Any resources that help
2. **Implementation Plan** — generate:

   * Full folder \& file structure
   * Implementation order (what gets built first)
   * Research context list (all links found)
   * Dependencies \& versions
   * Build system choice (Gradle vs Maven)
3. **User reviews the plan** → approve or adjust

**OUTPUT:** Implementation plan artifact + user approval.

\---

### Phase 6: Code Generation

> AI generates the FULL project. No placeholders, no TODOs, no shortcuts.

#### Build Order (IMPORTANT — follow this exact sequence):

**Step 1: Build System**

```
build.gradle (or pom.xml)
settings.gradle
gradle.properties
plugin.yml
gradle/wrapper/gradle-wrapper.properties
gradlew.bat (for Windows)
```

**Step 2: Utilities (no dependencies on other project classes)**

```
ColorUtil.java      → Color parsing (Hex, Legacy, MiniMessage)
PlaceholderUtil.java → PAPI wrapper with graceful fallback
```

**Step 3: Data Models (immutable, no logic)**

```
BroadcastData.java  → Title/Subtitle/Actionbar/Bossbar/Sound settings
PresetData.java     → Wraps BroadcastData + command name + permission
```

**Step 4: Managers (one responsibility each)**

```
ConfigManager.java    → YAML loading, preset parsing, message retrieval
DatabaseManager.java  → SQLite init, logging, history queries
CooldownManager.java  → UUID-based cooldown tracking
SchedulerManager.java → BukkitRunnable tasks for auto-broadcasts
PresetManager.java    → Dynamic command registration via CommandMap reflection
```

**Step 5: Senders (the display engine)**

```
BroadcastSender.java → Adventure API wrapper for all display types
```

**Step 6: Commands (the user-facing layer)**

```
BroadcastCommand.java → /bc <msg>, /bc reload, /bc history
PresetCommand.java    → Dynamic preset executor (extends Command, NOT CommandExecutor)
```

**Step 7: Main Class (wires everything together)**

```
BroadcastPlugin.java → onEnable/onDisable lifecycle
```

**Step 8: Resource Files**

```
config.yml    → General settings + preset definitions
messages.yml  → All translatable messages
sounds.yml    → Sound library shortcuts
```

#### Code Rules:

* **Every line must be production-ready.** No `// TODO`, no simplified code.
* **Use Adventure API natively** for all text (Paper has it built-in).
* **Use MiniMessage** for gradient/hover/click support.
* **PlaceholderAPI is a soft-dependency** — always check if it's loaded before calling it.
* **SQLite uses `java.sql`** — no external JDBC library needed.
* **Dynamic commands use reflection** on `Bukkit.getServer().getClass().getDeclaredField("commandMap")`.
* **PresetCommand extends `Command`**, NOT `CommandExecutor`. This is because `CommandExecutor` requires registration in `plugin.yml`, but presets are dynamic.

\---

### Phase 7: Verification Sweep

> Before compiling, do a mental sweep of the entire codebase.

Check for:

* ⚡ **Performance:** Are there any O(n²) loops? Unnecessary object creation?
* 🧠 **Logic:** Does the cooldown check happen before the broadcast sends?
* 🐛 **Bugs:** Null checks on config values? What if a preset has no sound section?
* 🔨 **Compilation:** Are all imports correct? Are all method signatures matching?
* 📝 **Style:** Consistent naming? No unused imports?

**DO NOT SKIP THIS PHASE.** Catching bugs here saves 30 minutes of recompile cycles.

\---

### Phase 8: Compile \& Test

> Build the JAR and test on a real server.

#### Compilation Steps:

1. Set `JAVA\_HOME` to the correct JDK path.
2. Run `./gradlew build` (or `gradlew.bat build` on Windows).
3. Check for `BUILD SUCCESSFUL` and locate the JAR in `build/libs/`.

#### Testing Steps:

1. Copy the JAR to the server's `plugins/` folder.
2. Start the server.
3. Check logs for `\[Broadcast] Enabling Broadcast v1.0.0`.
4. Test every command listed in the blueprint.
5. Check that config files are generated in `plugins/Broadcast/`.
6. Verify SQLite database is created.

#### If Compile Fails:

* **1-2 failures:** Fix and retry.
* **3 failures on the same issue:** Rewrite from Phase 3 (blueprint).

\---

## 🚨 Known Pitfalls \& Lessons Learned

These are real issues we encountered building the Broadcast plugin. Any future AI MUST be aware of these.

### 1\. JDK Version Incompatibility

**Problem:** User had JDK 25 installed. No version of Gradle supports JDK 25 yet.

* Gradle 8.5 → Kotlin DSL crashes parsing JDK 25 version string (`IllegalArgumentException: 25.0.1`)
* Gradle 8.14.2 → `Unsupported class file major version 69`
* Even Gradle 9.x has issues with JDK 25.

**Solution:**

* **Always check what JDK is installed FIRST** before setting up the build system.
* Run: `Get-ChildItem "C:\\Program Files\\Java" -Directory` and `echo $env:JAVA\_HOME`
* If only JDK 25 is available, check if JDK 21 exists elsewhere.
* **Use JDK 21 for building** (Paper 1.21.1 targets Java 21 anyway).
* Set JAVA\_HOME temporarily: `$env:JAVA\_HOME = "C:\\Program Files\\Java\\jdk-21"; .\\gradlew.bat build`

### 2\. JAVA\_HOME Pointing to `bin/` Instead of Root

**Problem:** `JAVA\_HOME` was set to `C:\\Program Files\\Java\\jdk-25\\bin` instead of `C:\\Program Files\\Java\\jdk-25`.

**Solution:** Always verify `JAVA\_HOME` points to the JDK root, not the `bin/` subdirectory.

### 3\. Kotlin DSL vs Groovy DSL

**Problem:** Gradle's Kotlin DSL (`build.gradle.kts`) uses an embedded Kotlin compiler that crashes on newer JDKs.

**Solution:** **Always use Groovy DSL** (`build.gradle`) for maximum compatibility. It's simpler and doesn't embed a Kotlin compiler.

```groovy
// build.gradle (Groovy — PREFERRED)
plugins {
    id 'java'
}

java {
    sourceCompatibility = JavaVersion.VERSION\_21
    targetCompatibility = JavaVersion.VERSION\_21
}
```

### 4\. EULA Agreement

**Problem:** Server won't start without `eula=true` in `eula.txt`.

**Solution:** Write `eula=true` to the file before starting:

```
Set-Content -Path "eula.txt" -Value "eula=true"
```

### 5\. JVM Flags in PowerShell

**Problem:** PowerShell parses `-D` flags differently than bash. Running:

```
\& java -Xmx2G -jar server.jar -Dcom.mojang.eula.agree=true
```

Fails because PowerShell treats `-D` as a java class name argument.

**Solution:** Put JVM flags BEFORE `-jar`, or better yet, just edit `eula.txt` directly.

### 6\. Gradle Wrapper Bootstrap

**Problem:** `gradle` command not found (not installed globally).

**Solution:** Bootstrap the wrapper manually:

1. Create `gradle/wrapper/gradle-wrapper.properties` with the distribution URL.
2. Download `gradle-wrapper.jar` from GitHub.
3. Create `gradlew.bat` script.

### 7\. Dynamic Command Registration on Reload

**Problem:** New presets added to `config.yml` after server start need their commands registered at runtime.

**Solution:** Use reflection to access `CommandMap`:

```java
Field commandMapField = Bukkit.getServer().getClass().getDeclaredField("commandMap");
commandMapField.setAccessible(true);
CommandMap commandMap = (CommandMap) commandMapField.get(Bukkit.getServer());
commandMap.register("pluginname", command);
```

For unregistration, also access `knownCommands`:

```java
Field knownCommandsField = commandMap.getClass().getDeclaredField("knownCommands");
knownCommandsField.setAccessible(true);
Map<String, Command> knownCommands = (Map<String, Command>) knownCommandsField.get(commandMap);
knownCommands.remove("pluginname:commandname");
knownCommands.remove("commandname");
```

### 8\. Adventure API is Native in Paper

**Problem:** Some guides tell you to shade Adventure API into your plugin.

**Solution:** **DON'T.** Paper 1.21.1 ships Adventure API natively. Just use `paper-api` as a `compileOnly` dependency and call the API directly:

```java
import net.kyori.adventure.text.Component;
import net.kyori.adventure.text.minimessage.MiniMessage;
import net.kyori.adventure.title.Title;

Component title = MiniMessage.miniMessage().deserialize("<gradient:gold:red>Hello!</gradient>");
player.showTitle(Title.title(title, subtitle, times));
```

### 9\. PlaceholderAPI Soft Dependency

**Problem:** Plugin crashes if PAPI isn't installed.

**Solution:** Always check before using:

```java
public static String parse(Player player, String text) {
    if (Bukkit.getPluginManager().getPlugin("PlaceholderAPI") != null) {
        return PlaceholderAPI.setPlaceholders(player, text);
    }
    return text; // graceful fallback
}
```

And in `plugin.yml`:

```yaml
softdepend: \[PlaceholderAPI]
```

### 10\. YAML Indentation Nightmares

**Problem:** User copied a preset config example but got the indentation wrong. Command didn't register.

**Lesson:** When providing config examples to users, always make it crystal clear how indentation works. Show the FULL context with surrounding lines so they know exactly where to paste.

\---

## 🛠️ Build System Reference

### Preferred Setup (Groovy Gradle)

```groovy
// build.gradle
plugins {
    id 'java'
}

group = 'me.namespace.pluginname'
version = '1.0.0'

java {
    sourceCompatibility = JavaVersion.VERSION\_21
    targetCompatibility = JavaVersion.VERSION\_21
}

repositories {
    mavenCentral()
    maven { url 'https://repo.papermc.io/repository/maven-public/' }
    maven { url 'https://repo.helpch.at/releases' }
}

dependencies {
    compileOnly 'io.papermc.paper:paper-api:1.21.1-R0.1-SNAPSHOT'
    compileOnly 'me.clip:placeholderapi:2.11.6'
}

tasks.named('processResources') {
    filesMatching('plugin.yml') {
        expand(version: project.version)
    }
}
```

### Gradle Wrapper Properties

```properties
distributionBase=GRADLE\_USER\_HOME
distributionPath=wrapper/dists
distributionUrl=https\\://services.gradle.org/distributions/gradle-8.14.2-bin.zip
networkTimeout=10000
validateDistributionUrl=true
zipStoreBase=GRADLE\_USER\_HOME
zipStorePath=wrapper/dists
```

### Compile Command (Windows PowerShell)

```powershell
$env:JAVA\_HOME = "C:\\Program Files\\Java\\jdk-21"; .\\gradlew.bat build
```

### Output Location

```
build/libs/PluginName-1.0.0.jar
```

\---

## 📋 plugin.yml Template

```yaml
name: PluginName
version: '${version}'
main: me.namespace.pluginname.MainClass
api-version: '1.21'
description: Plugin description here
authors: \[AuthorName]

softdepend: \[PlaceholderAPI]

commands:
  commandname:
    description: What it does
    usage: /<command> <args>
    aliases: \[alias1, alias2]

permissions:
  pluginname.use:
    description: Base permission
    default: true
  pluginname.admin:
    description: Admin permission
    default: op
```

\---

## 🧪 Testing Server Setup

### Option A: User's Local Server

1. User provides a server folder with `server.jar` and existing plugins.
2. Copy compiled JAR to `plugins/`.
3. Start with: `\& "C:\\Program Files\\Java\\jdk-21\\bin\\java.exe" -Xmx2G -jar server.jar --nogui`
4. Make sure `eula.txt` contains `eula=true`.
5. User joins and tests. Reports back with logs or mclog links.

### Option B: Docker (Clean Slate)

```yaml
# docker-compose.yml
version: '3.8'
services:
  mc:
    image: itzg/minecraft-server
    environment:
      TYPE: PAPER
      VERSION: "1.21.1"
      EULA: "TRUE"
      MEMORY: "2G"
    ports:
      - "25565:25565"
    volumes:
      - ./plugins:/data/plugins
```

\---

## ❌ Error Handling Matrix

|Situation|Action|
|-|-|
|No API found for a feature|Try another approach. If nothing works → notify user, suggest alternatives|
|Compile fails (1-2 times)|Fix and retry|
|Compile fails (3 times same issue)|Rewrite from Phase 3 (blueprint)|
|Runtime error in server logs|STOP, show error, ask user for input|
|Feature impossible on Paper|Notify user honestly, suggest alternatives|
|JDK version mismatch|Find a compatible JDK on the system, or ask user to install one|
|Gradle version too old|Upgrade wrapper to latest stable (currently 8.14.2)|
|Config not generating|Check that `saveDefaultConfig()` is called and resource files exist in `src/main/resources/`|
|Command not registering|Check `plugin.yml` for static commands, check `CommandMap` reflection for dynamic ones|
|PlaceholderAPI not parsing|Verify soft-dependency, check if PAPI jar is in server plugins|

\---

## 🧠 Architecture Patterns

### Manager Pattern

Every "system" gets its own Manager class:

* `ConfigManager` → loads/saves YAML
* `DatabaseManager` → SQLite operations
* `CooldownManager` → player cooldown tracking
* `SchedulerManager` → repeating tasks
* `PresetManager` → dynamic command registration

### Sender Pattern

Separate the "what to display" from "how to display it":

* `BroadcastData` = the data model (what title, what sound, etc.)
* `BroadcastSender` = the engine that actually shows it to players

### Soft Dependency Pattern

```java
// In onEnable:
if (Bukkit.getPluginManager().getPlugin("PlaceholderAPI") != null) {
    getLogger().info("Hooked into PlaceholderAPI!");
} else {
    getLogger().info("PlaceholderAPI not found, placeholders disabled.");
}
```

### Adventure API Color Pipeline

```
Raw Config String → Hex Replace (\&#RRGGBB → <color:#RRGGBB>) 
                  → Legacy Replace (\& → §) 
                  → MiniMessage Deserialize 
                  → Component (ready to send)
```

\---

## 📎 Quick Reference: Common Sound Names

|Sound|Bukkit Enum|
|-|-|
|XP Orb|`ENTITY\_EXPERIENCE\_ORB\_PICKUP`|
|Level Up|`ENTITY\_PLAYER\_LEVELUP`|
|Villager No|`ENTITY\_VILLAGER\_NO`|
|Note Block|`BLOCK\_NOTE\_BLOCK\_PLING`|
|Ender Dragon|`ENTITY\_ENDER\_DRAGON\_GROWL`|
|Wither Spawn|`ENTITY\_WITHER\_SPAWN`|
|Item Pickup|`ENTITY\_ITEM\_PICKUP`|
|Anvil Land|`BLOCK\_ANVIL\_LAND`|
|Chest Open|`BLOCK\_CHEST\_OPEN`|

Full list: https://hub.spigotmc.org/javadocs/bukkit/org/bukkit/Sound.html

\---

## 📎 Quick Reference: BossBar Colors \& Styles

**Colors:** `PINK`, `BLUE`, `RED`, `GREEN`, `YELLOW`, `PURPLE`, `WHITE`

**Styles:** `SOLID`, `SEGMENTED\_6`, `SEGMENTED\_10`, `SEGMENTED\_12`, `SEGMENTED\_20`

\---

## 🏁 Final Checklist Before Declaring "Done"

* \[ ] All commands from the blueprint work.
* \[ ] All config files generate on first run.
* \[ ] SQLite database creates and stores entries.
* \[ ] `/reload` command hot-reloads everything without restart.
* \[ ] New presets added via config work after reload.
* \[ ] Hex colors, legacy colors, and MiniMessage gradients all parse correctly.
* \[ ] PlaceholderAPI works if installed, fails gracefully if not.
* \[ ] Console can run all commands.
* \[ ] Cooldowns work when enabled.
* \[ ] Scheduled presets fire at the configured interval.
* \[ ] No errors in server console on startup.
* \[ ] JAR is in `build/libs/` and under 1MB (no shaded dependencies).

