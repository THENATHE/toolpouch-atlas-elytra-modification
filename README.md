# Tool Pouch Atlas & Elytra Modification

An **unofficial gameplay modification** for [Tool Pouch](https://github.com/pajicadvance/toolpouch): use MapStitch atlases from your pouch and toggle pouch-powered Elytra flight, and mend stored wings with collected XP. Delivered as a separate Fabric add-on, it leaves the original Tool Pouch and MapStitch JARs intact.

**Minecraft 26.3 · Fabric · Tool Pouch 1.1.10 · Optional MapStitch 1.1.6**

[Download the modification](https://github.com/THENATHE/toolpouch-atlas-elytra-modification/releases/latest) · [Companion Polymer shim](https://github.com/THENATHE/toolpouch-polymer-shim) · [Validation](docs/VALIDATION.md)

Tool Pouch and MapStitch are original projects by **pajic**. This repository contains the additional integration code, not the original mods. It is independently maintained by THENATHE and is not an official upstream release. Attribution and retained MIT notices are in [NOTICE.md](NOTICE.md) and [licenses/](licenses/).

## Features

### MapStitch atlases in the pouch

- Keeps atlases updating while stored in the active pouch, including an inventory pouch or a pouch attached to leggings.
- Makes stored atlases available to MapStitch's minimap and world map. Compass and clock checks also recognize items in the pouch.
- Saves atlas changes back into the correct pouch slot, supports map ejection, and preserves unrelated stored items.
- Avoids overwriting the mutable contents of an open pouch menu with an older stored snapshot.
- Respects inventory-use settings, atlas scanning settings, and the pouch selection supplied by Tool Pouch's own accessory abstraction.
- Migrates eligible legacy `improved-maps:atlas` allowance limits to `mapstitch:atlas` once. Existing MapStitch rules, migrated version-2 configurations, custom limits, and later administrator removals are preserved. This is a configuration migration, not a new Improved Maps integration.

- Invalidates cached minimap map centers when the client world or atlas metadata changes, avoiding stale locations after world transitions.

### Elytra toggle

- Adds an initially unbound **Toggle Tool Pouch Elytra** key under the existing Tool Pouch controls heading, preserving the key identifier used by the earlier combined patch. Version 1.0.1 fixes the duplicate heading by sharing the original category object.
- Adds `/toolpouch-elytra`, `/toolpouch-elytra on`, and `/toolpouch-elytra off` for your own pouch flight preference.
- Saves the preference per player, copies it on respawn, and synchronizes it to clients that have this modification. Synchronization is refreshed after player level changes.
- Disabling pouch flight leaves the wings stored and visible. Normal chest-slot Elytra remain usable with their normal durability handling.
- Detects the earlier integrated Tool Pouch toggle and patched MapStitch pouch bridge, and skips duplicate feature injections when those implementations are already present.

### XP Mending for stored Elytra

Version 1.0.2 lets damaged Mending Elytra directly inside the active pouch repair from collected experience. Equipped Mending items use XP first; eligible stored Elytra use the remainder, and unused XP goes to the player. Repair amounts and rounding follow vanilla enchantment effects.

Inventory pouches, leggings-attached pouches and open pouch menus are supported. Turning off pouch flight does not turn off Mending, and broken wings can repair. Pouch selection follows Tool Pouch's normal priority and inventory-use setting; unrelated contents are preserved.

**SSO's regular-Mending setting is respected.** If Simple Smithing Overhaul's Mending rework is enabled and `mendingRework.enableRegularMendingBehavior` is disabled, XP does not mend pouch Elytra either. Enable regular Mending in SSO when you want XP repair. This addon does not change your SSO configuration or require SSO to be installed.

## Install

Install `toolpouch-atlas-elytra-compat-1.0.2+26.3.jar` on **the server and participating modded clients**, alongside Tool Pouch 1.1.10 and its usual dependencies. Add MapStitch 1.1.6 when using the atlas features. The Elytra toggle works without MapStitch.

| Dependency | Tested version | Role |
| --- | --- | --- |
| Minecraft / Java | 26.3 / 25 | Runtime |
| Fabric Loader / Fabric API | 0.19.5 / 0.161.0+26.3 | Loader, events, commands, and networking |
| Tool Pouch | 1.1.10 | Required original mod |
| Fzzy Config | 0.7.7+fix2+26.3 | Tool Pouch configuration dependency |
| Fabric Language Kotlin | 1.14.1+kotlin.2.4.20 | Required by the tested dependency stack |
| MapStitch | 1.1.6 for 26.3 | Optional atlas integration |

Use one Tool Pouch JAR and one MapStitch JAR, with matching mod builds on the server and native clients. This add-on does not contain or replace either original mod.

For missing atlas seed-map centers and crafting corrections on a Multi-Shim server, also update [Multi-Shim](https://github.com/THENATHE/SSO-backpack-toolpouch-mapstitch-shim) to 1.0.3. The addon supplies controls/cache and XP Mending features; the server shim repairs atlas metadata/crafting and guards pouch-held shulkers against duplication and item loss.

### Vanilla players on the same server

This modification **does not require Polymer** and works on an ordinary modded server. To also allow players without Tool Pouch to join, install the separate [Tool Pouch Polymer Shim](https://github.com/THENATHE/toolpouch-polymer-shim) and Polymer on the server. If MapStitch is installed, add the [MapStitch Polymer Shim](https://github.com/THENATHE/mapstitch-polymer-shim) as well.

The modification provides the gameplay changes. The Polymer shim handles client compatibility: native players use their normal mod interfaces, while unsupported clients receive display items and an install notice. Either add-on can be used independently, and both have been tested together.

## How it works

Fabric Mixins connect Tool Pouch's active-container lookup to MapStitch's atlas ticking, map lookup, ejection, and rendering paths. Changes are written back to the original item components; the add-on registers no replacement pouch or atlas item. XP Mending extends the original experience-orb repair method after equipped items have been handled, so SSO can disable the complete XP repair pass. Open pouch menus are updated through their live inventory; closed pouches are updated at the selected item slot. The flight toggle uses a persisted player preference and an optional custom synchronization payload, so the standalone implementation does not add custom vanilla player entity metadata.

Startup feature detection inspects the installed mods before applying the relevant mixins. Already-integrated toggle or atlas code remains responsible for that feature, avoiding duplicate hooks. The add-on never rewrites a dependency JAR.

## Direct compatibility layers

| Project | What this modification directly integrates |
| --- | --- |
| [Tool Pouch](https://github.com/pajicadvance/toolpouch) | Active pouch selection, stored contents, allowed-item rules, flight lookup, durability behavior, and player preference persistence. |
| [MapStitch](https://github.com/pajicadvance/mapstitch) | Stored atlas ticking, saveback, ejection, minimap/world-map lookup, and compass/clock discovery. |

[Tool Pouch Polymer Shim](https://github.com/THENATHE/toolpouch-polymer-shim) is the tested companion for mixed native/vanilla servers; it is not a required dependency or a dedicated mixin target here. Support for accessory slots is inherited through Tool Pouch. This project does not claim new dedicated layers for Trinkets, Curios, Ohmega, Aileron, or Shared Region Maps.

## Build and validation

Use a Java 25 JDK. Place the compile-only dependency JARs listed in [libs/README.md](libs/README.md) in `libs/`, then run:

```sh
./gradlew build
```

Windows: `gradlew.bat build`. Output: `build/libs/toolpouch-atlas-elytra-compat-1.0.2+26.3.jar`. Dependencies are not bundled. A newer compiler can target Java 25 with `-PcompilerVersion=27` while Gradle runs on Java 25.

Version 1.0.2 adds actual XP-orb pickup tests, SSO-enabled/disabled controls, XP accounting, live-menu persistence and existing controls/atlas regressions. The original 1.0.0 record retains its 208 historical assertions. Read [the validation record](docs/VALIDATION.md) for exact tested artifacts, results and limits. Report problems specific to this modification in [this repository's issues](https://github.com/THENATHE/toolpouch-atlas-elytra-modification/issues).
