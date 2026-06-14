![Mekanism Logo](logo.png)

# Mekanism 26.1.2 Update Fork

This fork tracks Mekanism's `26.1` branch and documents the local changes made to run against Minecraft `26.1.2`.

## Update Scope

- Target Minecraft version: `26.1.2`
- Target NeoForge version range: `26.1.2.x`
- Java toolchain: `25`
- Gradle: `9.5.0`
- ModDevGradle: `2.x`

## Local Compatibility Changes

- Added a temporary Iris `1.10.9+mc26.1.1` guard for MekaSuit custom armor rendering.
- Added temporary renderer bridge methods for Iris' old Mekanism Flame and SPS mixin targets.
- No KubeJS or modpack script changes are included in this repository.

## Build

```powershell
./gradlew.bat build
```

Built jars are written to:

```text
build/libs/
```

## Notes

These changes are intended only as a compatibility update for Minecraft `26.1.2`. Remove the Iris workarounds once Iris updates its Mekanism compatibility mixins for the new renderer signatures.
