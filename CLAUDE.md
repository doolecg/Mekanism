# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Mekanism is a large NeoForge tech mod for Minecraft. This branch (`26.1`) targets **Minecraft 26.1.2**, **NeoForge 26.1.2.74**, and **Java 25**. The mod ships as one codebase that builds into multiple jars: the core `Mekanism` mod plus three secondary modules — `additions`, `generators`, and `tools` — each its own NeoForge mod that depends on core.

## Common commands

Use the Gradle wrapper (`./gradlew` / `gradlew.bat`). Build is configured for JDK 25 toolchain but Gradle itself needs JDK 17+.

- `gradlew build` — compile all modules and assemble the jars into `build/libs/`.
- `gradlew test` — run JUnit 6 / jqwik unit tests (`src/test`). Filter with `gradlew test --tests "mekanism.SomeTest"`.
- `gradlew runClient` — launch the dev client. Add `-Pmc_devlogin=true` (or set `mc_devlogin` in your Gradle home properties) to log into a real account.
- `gradlew runServer` — launch a dev dedicated server.
- `gradlew runClientAlt` — second client instance with username `AltDev` (for multiplayer testing).
- `gradlew runData` — run data generation. **Required after changing any datagen code**; writes into `src/datagen/generated/`. Generated output is committed.
- `gradlew runGameTestServer` — run in-game GameTests (`src/gameTest`) headlessly.
- `gradlew runGameTestClient` — launch a client into the GameTest world for manual inspection.
- `gradlew runDataGameTest` — generate GameTest structures/data into `src/gameTest/generated/`.

## Source layout

Each module lives under `src/<module>/`; the main mod is `src/main`. Java for every module is under the `mekanism` package root (secondary modules use `mekanism.additions`, `mekanism.generators`, `mekanism.tools`).

- `src/api` — the public API jar (`mekanism.api.*`). No resources. Keep this stable and dependency-light; it is published for other mod developers.
- `src/main/java/mekanism/common` — server/common logic (blocks, tiles, items, recipes, networking, config, capabilities, integrations).
- `src/main/java/mekanism/client` — client-only rendering, GUIs, sounds, recipe-viewer (JEI/EMI) integration.
- `src/datagen/<module>` — data generators; output goes to `src/datagen/generated/<modid>`.
- `src/test` — JUnit/jqwik unit tests. `src/gameTest` — in-world GameTests.
- `annotation-processor/` — a standalone Gradle subproject (the only entry in `settings.gradle`) containing Mekanism's compile-time annotation processors.
- `buildSrc/` — custom Gradle task types (`AllJar`, `MergeModuleResources`, `OptimizePng`, `OutputChangelog`, etc.).
- `docs/Development.md` — authoritative directory/packaging breakdown; `docs/Mekanism.xml` — IntelliJ code style.

## Architecture notes

**Registration.** Content is registered through Mekanism's own deferred-register wrappers in `mekanism.common.registration` (e.g. `MekanismDeferredRegister`, `DoubleDeferredRegister`, `DatapackDeferredRegister`), not vanilla `DeferredRegister` directly. Each registry has a central holder class in `mekanism.common.registries` (`MekanismBlocks`, `MekanismItems`, `MekanismTileEntityTypes`, `MekanismChemicals`, `MekanismDataComponents`, …). When adding content, add it to the relevant `Mekanism*` registry class. Blocks are described declaratively via `BlockType`s in `mekanism.common.content.blocktype`.

**Multi-module build.** `build.gradle` programmatically creates source sets and configurations for `additions`/`generators`/`tools`, each with matching `datagen` and `gameTest` extra source sets, all sharing core + api on their classpath. Secondary modules' `implementation`/`compileOnly`/etc. configurations extend the core ones. Don't hand-edit per-module config — extend the existing `setupExtraSourceSets` machinery.

**Annotation processors** (`annotation-processor/`): `ComputerMethodProcessor` generates ComputerCraft/OpenComputers peripheral bindings from `@ComputerMethod` annotations; `MethodFactoryProcessor` and the param-name mapper generate dispatch glue. The processor is wired in via the `-AmekanismModule=...` compiler arg per source set. Generated sources land in `build/generated/sources/annotationProcessor/`.

**Integrations** live in `mekanism.common.integration` (and `mekanism.client.recipe_viewer` for viewers). Several are gated by `disable*` flags near the top of `build.gradle` (`disableCrT`, `disableTOP`, `disableEMI`, `disableProjectE`) because their upstream deps haven't updated for this MC version — the matching packages are `exclude`d from compilation. Re-enable a flag (and the corresponding `localRuntime`/`runtimeOnly` deps, currently commented with `TODO - 26.1`) once the dependency updates.

## Porting conventions

This branch is an in-progress port. `gradle.properties` tracks `previous_minecraft_version` / `previous_minor_minecraft_version` as porting helpers, and some dependencies are still compiled against the previous MC version. Code/build comments tagged `TODO - 26.1:` or `TODO - 1.21.11:` mark deferred porting work. Respect existing `disable*` integration flags rather than forcing disabled integrations to compile.

## Conventions

- Match the IntelliJ style in `docs/Mekanism.xml`. User-facing strings go through `MekanismLang` (and per-module `*Lang`) translation enums, not hardcoded literals.
- After editing recipes, tags, loot, advancements, or other generated data, run `gradlew runData` and commit the regenerated `src/datagen/generated/` output.
- `processResources` re-serializes (minifies) every JSON/`.mcmeta` and `FAIL`s on duplicate resources across merged modules — keep resource paths unique per module.
