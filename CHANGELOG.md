# Changelog

## [1.4.0] - 2026-08-04

- Limit every hand-written class, including record classes and allowed partial aggregates, to 1500 physical lines.
- Allow only one top-level type per hand-written C# file while retaining genuinely owned nested types.
- Require touched legacy violations to be split or responsibility-extracted without authorizing unrelated repository-wide cleanup.

## [1.3.0] - 2026-08-04

- Make compatibility opt-in per current user request and require the current authoritative contract by default.
- Define the only standing exception as Unity-version compatibility for user-maintained reusable Unity packages, plugins, and Editor extensions across their declared support range.
- Prevent the Unity-version exception from preserving legacy plugin APIs, routes, schemas, responses, revisions, protocols, data, or behavior.

## [1.2.0] - 2026-08-03

- Prohibit creating or expanding hand-written partial types and treating file movement as responsibility separation.
- Require aggregate review and responsibility extraction when existing hand-written partial types are touched.
- Make the shared partial-type prohibition override per-file size and organization rules, with a narrow generated-code exception.

## [1.1.1] - 2026-08-03

- Make the shared small-change rule the explicit validation authority for every consumer-project change.
- Move the common source-file EOF and Prefab responsibility-container conventions into the shared baseline.
- Remove the obsolete reference to project-local small-change validation sections.

## [1.1.0] - 2026-08-03

- Add shared code-quality, Unity Editor safety/evidence, Prefab/serialization, package/plugin, and localization contracts.
- Make static-only delivery the common default for small changes in both consumer projects.
- Unify Unity Safe Mode handling and keep project-specific permissions and product facts in local overlays.

## [1.0.1] - 2026-08-03

- Reduce the README to usage and release instructions.

## [1.0.0] - 2026-08-03

- Publish the shared Unity MCP instruction contract for BattleIdle and MarbleBattlers.
- Define submodule-based consumption, project-local routing, conflict boundaries, compact response rules, and risk-tiered MCP regression policy.
