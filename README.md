![Mekanism Logo](logo.png)

# Mekanism 26.1.2 Update Fork

This fork tracks Mekanism's `26.1` branch and documents the local changes made to run against Minecraft `26.1.2`. It is a personal compatibility/port effort, not an official Mekanism release.

## Update Scope

- Target Minecraft version: `26.1.2`
- Target NeoForge version: `26.1.2.74` (range `[26.1.2.74,)`)
- Java toolchain: `25`
- Gradle: `9.5.0`
- ModDevGradle: `2.x`

## Status

- The full build (core `mekanism` plus the `additions`, `generators`, and `tools` modules, including the datagen and gameTest source sets) compiles.
- The unit / jqwik test suite passes.
- The mod loads in-game on Minecraft `26.1.2` / NeoForge `26.1.2.74`.

## Known Limitations

These integrations are currently compile-disabled (via the `disable*` flags in `build.gradle`) because their upstream mods have not yet released for `26.1.2`. They will be re-enabled as those dependencies update — search the source for `TODO - 26.1` markers near each:

- CraftTweaker (`disableCrT`)
- The One Probe (`disableTOP`)
- EMI (`disableEMI`)
- ProjectE (`disableProjectE`)

Broader port cleanup is tracked inline with `TODO - 26.1` comments throughout the source.

## Local Compatibility Changes

- Added a temporary Iris `1.10.9+mc26.1.1` guard for MekaSuit custom armor rendering.
- Added temporary renderer bridge methods for Iris' old Mekanism Flame and SPS mixin targets.
- No KubeJS or modpack script changes are included in this repository.

## Build

```powershell
./gradlew.bat build
```

Built jars are written to `build/libs/`.

To launch a development client or regenerate data:

```powershell
./gradlew.bat runClient
./gradlew.bat runData
```

See [CLAUDE.md](CLAUDE.md) for a fuller breakdown of the build commands and code architecture.

## Notes

These changes are intended only as a compatibility update for Minecraft `26.1.2`. Remove the Iris workarounds once Iris updates its Mekanism compatibility mixins for the new renderer signatures.

Mekanism is licensed under the MIT license. Copyright 2017-2025 Aidan C. Brady. See the upstream [Mekanism repository](https://github.com/mekanism/Mekanism) for the original project, full credits, and license text.
