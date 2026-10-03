# Validation — 1.0.2+26.3

Recorded 2026-10-02. Exact addon SHA-256:

`e08a8a45c2a89b4a7d64fadf27dd20e6a2bf1b1635da50c64333b8bd42de3748`

Minecraft 26.3, Java 25, Fabric Loader 0.19.5, Fabric API 0.161.0+26.3, original Tool Pouch 1.1.10+26.3. Mixed profiles use unchanged Multi-Shim 1.0.3 (`d5d08c6a5164206c9c2d47a58a89fec5f56ae03c7fcb12cbe04c98ed7d259722`). Original mod/dependency JARs remain unchanged; exact client/server hashes are in each evidence record. [Compile input inventory](1.0.2/compile-inputs.json).

## XP Mending behavior and tests

Vanilla's equipment scan does not include Elytra stored in a pouch. The original addon 1.0.1 control confirmed the gap: real XP orbs credited the player while stored Mending Elytra remained damaged. Version 1.0.2 repairs directly stored Elytra using XP left after the regular equipment pass, with vanilla repair-effect evaluation and integer XP accounting.

**The user's selected policy is to follow SSO's regular-Mending setting.** With SSO's Mending rework enabled and `enableRegularMendingBehavior=false` (the tested default), neither equipped nor pouch items mend from XP; the player receives it. With regular Mending enabled, pouch repair works. The addon does not edit that setting or require SSO. Its hook is inside the vanilla method body, which SSO's disabled-Mending wrapper skips. Both exact SSO targets verified this interaction at runtime.

Each full profile performed 22 actual ExperienceOrb pickups, 12 authoritative durability/XP checks and 12 settled-client comparisons: **46 checks**. Orbs were spawned at the connected native player and collected by normal collision, with actual `playerTouch` tracing; the production repair helper was not invoked directly.

| Profile | Result and evidence |
| --- | --- |
| Prior addon 1.0.1, isolated Tool Pouch | 46 expected-control checks passed, documenting absent pouch repair while ordinary worn-item Mending worked. [Baseline](1.0.2/xp-baseline.json). |
| New addon, isolated Tool Pouch | 46 passed without SSO, MapStitch or Polymer. [Evidence](1.0.2/xp-isolated.json). |
| Developer SSO 2.9.14, regular Mending disabled | 46 passed: no XP repair, ordinary XP credit retained. [Evidence](1.0.2/xp-developer-disabled.json). |
| Developer SSO 2.9.14, regular Mending enabled | 46 passed: pouch repairs and XP accounting correct. [Evidence](1.0.2/xp-developer-enabled.json). |
| Existing SSO 2.9.14-port.1, regular Mending disabled | 46 independently passed with its separate dependencies. [Evidence](1.0.2/xp-port-disabled.json). |
| Existing SSO port, regular Mending enabled | 46 independently passed: same requested behavior. [Evidence](1.0.2/xp-port-enabled.json). |
| Saved-world process restarts | All five candidate profiles passed a full server/client restart without reseeding, preserving durability, XP and client agreement. [Isolated](1.0.2/xp-isolated-reconnect.json), [developer disabled](1.0.2/xp-developer-disabled-reconnect.json), [developer enabled](1.0.2/xp-developer-enabled-reconnect.json), [port disabled](1.0.2/xp-port-disabled-reconnect.json), [port enabled](1.0.2/xp-port-enabled-reconnect.json). |

The 12 scenarios cover inventory/leggings pouches; flight toggle off; unenchanted/full/loose-inventory negative controls; 5-damage repair with XP remainder; broken Elytra; equipped-item priority; multiple stored wings; an open parent pouch menu; and a final leggings state for restart. A 7-XP orb repairs 14 damage normally. Repairing 5 damage consumes 2 XP and awards 5; two items with 80 total damage consume 40 out of 42 collected XP. Open-menu repair changes the live container and survives its close/save, preserving the unrelated clock. Settings changes belong only to the disposable test server's memory.

## Existing feature regressions

| Check | Result |
| --- | --- |
| Controls/flight with MapStitch | 101 assertions passed: original category identity, key/payload, inventory/leggings flight, chest fallback, commands, respawn, process restart/rejoin and dimension change. [Evidence](1.0.2/elytra-with-mapstitch.json). |
| Controls/flight without MapStitch | The same 101 assertions independently passed. [Evidence](1.0.2/elytra-without-mapstitch.json). |
| Atlas, minimap and crafting | 39 client plus 38 server assertions passed with the exact addon and current combined shim. [Evidence](1.0.2/atlas-with-mapstitch.json). |

Build passed using Java 25 Gradle with `-PcompilerVersion=27` and Java 25 bytecode output. Independent source review covered active-pouch selection, live-menu persistence, unchanged unrelated parent slots, bounded zero-effect handling, XP accounting, SSO wrapper interaction and optional mixin gating. No class-name collisions exist between the addon and current combined shim. New Mending hooks are independent of the native-toggle feature detector.

## Reproduction and scope

The [XP fixture](../qa/pouch-mending/README.md) records exact inputs and expected behavior. The existing [controls/atlas fixture](https://github.com/THENATHE/SSO-backpack-toolpouch-mapstitch-shim/tree/v1.0.3%2B26.3/qa/addon) supplies broader regressions. Original libraries, credentials, raw launch commands and disposable worlds remain excluded from published source; no user world/configuration was changed.

This covers normal Elytra directly in the active pouch. Elytra nested inside another container, custom replacement glider items, third-party accessory API runtime behavior and arbitrary datapack enchantment conditions were not tested. Eligibility uses the normal chest-slot repair-with-XP enchantment effect; zero/nonpositive effects are skipped without an unbounded loop. Equipment retains priority, and the active-pouch lookup retains Tool Pouch's inventory setting and companion shim's native-client guard. The latter was source-reviewed here; true-vanilla XP pickups were not part of this native feature suite.

Tool Pouch has no separate Minecraft version port in this release inventory. Developer SSO and the existing SSO port are independently tested optional integrations; SSO is not a new required dependency. Previous addon versions and their original source/release records remain preserved. Historical validation below refers to the older artifact hashes, not new 1.0.2 runs.

---

# Validation — 1.0.1+26.3

Recorded 2026-10-02 for Fabric Minecraft 26.3. Release SHA-256:

`77dc668b164bca182eec1e558b2948bd98ee40545ef30881bcdd471f43af1b0a`

| Scenario | Result |
| --- | --- |
| Original addon controls baseline | Actual vanilla KeyBindsList contained two Tool Pouch headings. [Evidence](keybind-heading-baseline.json). |
| Final addon with original Tool Pouch and MapStitch | 101 assertions passed: exactly one heading, one registered toggle sharing the original category; normal key/payload state, inventory/leggings flight, cosmetic wings, chest-slot fallback, respawn, full restart/rejoin, nonoperator on/off/toggle commands, and real Nether transition resynchronization. [Evidence](keybind-elytra-with-mapstitch.json). |
| Final addon without optional MapStitch | The same 101 assertions passed, independently exercising optional mixin gating. [Evidence](keybind-elytra-without-mapstitch.json). |

These tests used the exact addon above and combined-shim candidate `fc756498299259767766054a9c817a4d183c7467c5b62c342eadde92b8554926`. The server's later capacity-preserving atlas repair change does not change addon bytecode or its elytra paths. Exact server/client dependency hashes are retained in each result. The final atlas integration used this addon with release server SHA `d71a659db9c2025542953839facc5263cf2f80085644608a753913d50786e7d4` and passed 39 client plus 38 server assertions: actual minimap/world-map rendering and terrain updates in inventory/leggings pouches, authoritative native crafting, metadata/large-content preservation, and world-transition cache invalidation. [Exact atlas evidence](atlas.json), [baseline reproductions](atlas-baseline.json). The cache test uses a stale non-atlas sentinel and a deliberately wrong center during real Nether travel, then verifies reconstruction on return; a legitimately repopulated cache is allowed.

The client cache fix invalidates minimap centers when the client world or atlas metadata changes. It is independently gated by MapStitch's presence, including installations with an already-integrated pouch bridge. Original Tool Pouch and MapStitch JARs are unchanged. The original dependencies, saved preferences and key identifier are preserved.

Reproducible current fixtures: [Multi-Shim addon QA](https://github.com/THENATHE/SSO-backpack-toolpouch-mapstitch-shim/tree/main/qa/addon). They use disposable localhost profiles and the workspace's existing game libraries; original game/mod JARs, worlds and raw launch audits are not republished.

Not tested: physical keyboard hardware, sustained rocket flight, third-party accessory/Aileron integrations, arbitrary client mods, or all server configurations. The standalone original-addon checks below are historical 1.0.0 results, not claimed as fresh 1.0.1 runs.

---

# Validation — 1.0.0+26.3

Recorded 2026-09-30 for Fabric Minecraft 26.3. Release SHA-256:

`9969d1e8e4debb7e85832137add1fc5f5ae49c2f235469a9f95dacc9e3bc7be6`

| Scenario | Assertions | Result |
| --- | ---: | --- |
| Original Tool Pouch + modification; no MapStitch or Polymer | 41 | Passed dedicated Elytra checks |
| Original Tool Pouch + original MapStitch + modification; no Polymer | 75 | Passed dedicated atlas checks |
| Native client/server with original Tool Pouch + modification; no Polymer | 68 | Passed keybind, custom payload, respawn, and restart/reconnect checks |
| Native atlas client/server with original mods + modification and both Polymer shims on the server | 24 | Passed minimap/world-map rendering and item-discovery checks |

**208 assertions passed.** The dedicated suites were rerun against the final release hash. The original Tool Pouch and MapStitch files were not changed.

Coverage includes flight eligibility, midflight disable logic, chest Elytra durability, retained cosmetic wings, inventory/leggings pouch selection, broken wings, player isolation, saved preferences, allowance migration, opt-outs, map allocation/ticking/ejection, multiple atlases, open-menu snapshot protection, and compass/clock lookup. Registered key presses were dispatched programmatically through the normal network path.

The companion Polymer shim additionally passed native menu extraction/reinsertion, mixed Tool Pouch/MapStitch client capability tests, unmodified vanilla connections, and equipped-armor notices. See [its validation record](https://github.com/THENATHE/toolpouch-polymer-shim/blob/main/docs/VALIDATION.md).

Not runtime-tested: physical keyboard hardware, sustained real-world flying/rocket boosting, third-party accessory/Aileron integrations, or explicit Nether/End travel. Level-change synchronization invalidation is implemented and compiled; respawn and reconnect were exercised. These results do not establish compatibility with other Minecraft/Fabric/Polymer versions.

The runtime harness used local dedicated-server/client installations and is not a portable part of this repository. `./gradlew build` checks compilation and packaging; it does not rerun those gameplay tests. Test worlds, dependency JARs, account/environment paths, and raw runtime logs are intentionally not published.
