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
