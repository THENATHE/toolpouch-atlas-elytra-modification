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
