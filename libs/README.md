# Compile-only dependencies

Obtain these original artifacts from their publishers and place them in this directory before building. They are ignored by Git and are not included in the output JAR.

| Filename | Source |
| --- | --- |
| `toolpouch-fabric-1.1.10+26.3.jar` | [Tool Pouch](https://modrinth.com/mod/tool-pouch), [source](https://github.com/pajicadvance/toolpouch) |
| `mapstitch-fabric-1.1.6+26.3.jar` | [MapStitch source and downloads](https://github.com/pajicadvance/mapstitch) |
| `fzzy_config-0.7.7+fix2+26.3.jar` | [Fzzy Config](https://modrinth.com/mod/fzzy-config) |
| `kotlin-stdlib-2.4.20.jar` | [Kotlin standard library on Maven Central](https://repo.maven.apache.org/maven2/org/jetbrains/kotlin/kotlin-stdlib/2.4.20/) |

MapStitch is needed to compile the optional integration even when you only intend to use the Elytra feature at runtime. Gradle downloads Minecraft, Fabric Loader, and Fabric API from their configured repositories. Use the specified Minecraft release and dependency versions; arbitrary substitutions are not a tested build.
