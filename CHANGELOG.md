# Changelog

## [1.28.0] - 2026-08-17

- Treat the subject, ownership, object type and scope already established by the actual consumer UI as omittable player-copy context.
- Require descriptions to state only new effects, conditions, targets, ranges, durations and exceptions, retaining an explicit subject only when omission would create ambiguity or change scope.
- Require every Locale to preserve equivalent information necessity instead of reintroducing redundant UI context through literal translation.

## [1.27.0] - 2026-08-17

- Make `IGameItem.IsDestroyed` the sole authority for whether a GameItem still exists, has returned to its pool, or remains safe to reference as a GameItem.
- Prohibit using `GameObject.activeSelf`, `activeInHierarchy`, component enablement, hierarchy, rendering or collision state as GameItem lifecycle proxies.
- Keep independent domain qualifications such as `IsInGame`, ownership, container membership and faction scoped to their own authoritative contracts.

## [1.26.0] - 2026-08-17

- Prohibit creating, recommending or switching to extra branches, worktrees or PR workflows unless the current user explicitly requests them.
- Clarify that dirty worktrees, isolation, commits, pushes, review convenience, branch prefixes and PR-capable tools never grant branch-creation authority.
- Allow consumer projects to tighten the shared baseline to a single branch such as `main`, requiring existing work to be preserved and merged before extra branches are removed.

## [1.25.0] - 2026-08-17

- Prohibit two or more independently assignable ordinary simple-class selectors from repeating the same bundle of two or more property/value declarations.
- Require one semantic shared class to own the common declarations and be assigned to every affected UXML or runtime consumer, while pseudo-state and relational selectors remain independent contracts.
- Require first-class USS auditors to report this violation as an unsuppressible error with related selectors, repeated declarations, `passed=false`, and a separate `errorCount`.

## [1.24.0] - 2026-08-16

- Permanently prohibit comma-separated selector lists in USS, including groups whose declarations are identical.
- Require normal, pseudo-state and combined-state selectors to use independent blocks rather than grouped selectors for deduplication.
- Require every touched legacy grouped block to be fully split and all added or modified USS to pass a zero-selector-list static check before delivery.

## [1.23.0] - 2026-08-16

- Require generated UI Toolkit parts to be skinned from their actual runtime hierarchy, pseudo-state owner and current engine-theme winning selector rather than inferred outer-control states.
- Require state overrides for reused checkmarks, arrows and similar parts to cover only evidenced theme branches with sufficient specificity and stylesheet order, including meaningful combined states when they rewrite the same property.
- Require disproved selectors to be removed before correction and keep reusable state skins in the control or variant USS while project-specific assets remain local.

## [1.22.0] - 2026-08-16

- Make a current authorized task's proven dependency on a missing or defective first-class capability in a user-maintained MCP plugin/server sufficient to enter the authoritative repair flow without a second authorization request.
- Define authoritative repair, risk-matched focused verification, the existing release mechanism, direct consumer pin/lock updates and formal host activation as one narrowly authorized implementation transaction.
- Give this explicit MCP/package composition precedence over general implementation-versus-publication separation while retaining all boundaries for consumer business commits, product releases, PRs, third-party repositories, destructive cleanup, breaking changes and runtime or visual validation.

## [1.21.0] - 2026-08-16

- Make the actual consumer UI and its nearest player-facing copy family the vocabulary authority for names, descriptions, tooltips, controls, prompts and status text.
- Prohibit undisplayed internal properties, fields, derived ratios, conversion products, intermediate state, serialization identifiers and algorithm stages from leaking into player-facing copy.
- Require copy without an established term to describe observable results through existing UI concepts, and require a complete localized UI consumer contract before introducing a new product term.

## [1.20.0] - 2026-08-15

- Require explicitly authorized, runtime-replaced UI Builder previews to use deterministic, varied samples covering every current finite semantic class, serialized setting, template/container structure, USS selector, layout style, state asset and meaningful combined state.
- Require preview samples to be updated in the same task whenever their runtime generation chain or represented visual contract changes, with a searchable marker naming the preview purpose and clearing owner.
- Clarify that this contract does not authorize production mocks and that runtime owners must clear and rebuild previews without consuming them as data, configuration, defaults or fallback.

## [1.19.0] - 2026-08-15

- Require authoring-controlled integration identifiers that may vary by instance, host, template, skin or Variant to be serialized by the behavior owner or its explicit configuration and supplied by the corresponding Prefab or Asset.
- Prohibit hiding those identifiers in constants, static values, method literals or global settings, while retaining constants for truly internal, instance-invariant or framework-fixed identifiers.
- Require migrations to remove the former literal or constant so configuration and code cannot remain competing authorities.

## [1.18.0] - 2026-08-15

- Make a numeric `Static Cost Ledger` a hard pre-write gate for multi-axis loops, exhaustive search, event/geometry enumeration, full scans and per-item expensive owner calls in production, probes, tests, Gizmos and Editor tools.
- Require the ledger to expose real nesting products, frozen-input maxima, total expensive-call counts, peak allocation/lifetime, thread and synchronization ownership, main-thread occupancy, cache invalidation, budget source and an explicit pass/fail result; unknown or over-budget terms now block implementation, compilation and runtime experiments.
- Invalidate the ledger whenever implementation loops, nesting, domains, threads, caches or expensive calls change, and prohibit compilation success, average timing, expected early exit, asynchronous execution or user experiments from substituting for static proof.

## [1.17.0] - 2026-08-13

- Require content and behavior variants that preserve ownership, lifecycle and processing semantics to reuse the existing authoritative pipeline instead of adding per-asset, per-prefab or per-consumer components and side paths.
- Permit specialization only for evidenced semantic, ownership, lifecycle, state, permission, data, interaction, rendering or failure-contract differences, with shared processing reconverging after the true decision boundary.
- Require demonstrably unused components and their dedicated fields, references, nodes, assets and tests to be removed from the current affected chain, while distinguishing concrete future contracts from speculative reuse and preserving task-scope, user-data and public-contract boundaries.

## [1.16.0] - 2026-08-13

- Treat insufficient precision, missing fields and uncorrelated diagnostic stages as observability defects that must be repaired at the authoritative evidence producer instead of left as manual guesswork or scattered logs.
- Require continuous geometry, probability, search and optimization work to define equations, exact feasible domains, shared visualizations, objectives and algorithmic cost before production implementation.
- Make confirmed Bugs in the currently authorized system the highest-priority implementation work, while preserving owner evidence, safety and validation permission boundaries.

## [1.15.0] - 2026-08-13

- Prohibit every local filesystem dependency for Unity packages, plugins, Unity MCP packages and their consumers, including temporary development pins, `file:`, `source: local`, embedded overrides, symlinks and junctions.
- Require consumers to use a registry version or a remote Git URL pinned to a full immutable commit SHA after the authoritative package has been published.
- Require pre- and post-change scans of package metadata, manifest/lock files, UPM installation configuration and dependency examples across every controlled consumer, with any local dependency blocking completion.

## [1.14.0] - 2026-08-13

- Treat a UI semantic rename as one atomic migration across UXML/Prefab identifiers, selectors, query paths, dedicated visual-asset filenames, internal Texture/Sprite/Object names and every consumer reference.
- Require full state-asset families to migrate together while preserving Unity GUID/local-file-ID identity, and prohibit reference swapping, reimported GUIDs, stale aliases or old/new naming tracks.
- Preserve accurately named neutral canonical assets shared by unrelated UI consumers, while requiring misleading shared business names to be migrated only after auditing every consumer.

## [1.13.0] - 2026-08-12

- Separate ordinary consumer-project publication permission from user-maintained package/plugin publication permission.
- Make every authorized modification to a user-maintained package/plugin automatically complete its established release flow and affected consumer pin updates, without a second push request.
- Limit that standing authorization to the package release and directly related consumer pin/lock/install changes, excluding unrelated project changes, product releases, PRs and invented release channels.

## [1.12.0] - 2026-08-11

- Require an already known exact witness to be replayed through the same production pipeline before broad candidate, route or call-chain investigation, with structured per-stage inputs, outputs and the first rejection boundary.
- Clarify that exact-witness-first controls diagnostic ordering only and does not replace frozen-build scenario collection, complete owner/state matrices, propagation proof or competing-explanation elimination.
- Prohibit diagnostic reimplementation, fabricated inputs, parameter/ranking experiments and unbounded enumeration before the concrete witness path is exhausted.

## [1.11.0] - 2026-08-09

- Treat missing, stale or host-unregistered first-class MCP tools and incomplete authoritative Editor state as capability defects that must enter the authorized plugin repair flow.
- Prohibit Computer Use, mouse, keyboard, Editor-menu, generic-executor, direct-HTTP, CLI, project-script and file-operation substitutes for an affected MCP capability.
- Require end-to-end evidence from the published revision and active process through catalog activation, `tools/list_changed`, host registration and direct typed invocation.
- Define an independent standard MCP client as an isolation boundary rather than target-host acceptance, requiring formal reconnect/restart and target-host readback before completion.

## [1.10.1] - 2026-08-09

- Clarify that a clean detached submodule at an exact published gitlink is the expected consumer state.
- Prohibit only dirty submodules and uncommitted shared-rule edits left on detached HEAD, reconciling the maintainer contract with the documented consumer update command.

## [1.10.0] - 2026-08-09

- Require authorized package, plugin and MCP migrations to inventory authoritative, temporary and superseded paths before creating a replacement, then retain only one current installation per logical entry.
- Require exact-path safety checks, dirty/untracked ownership review, deletion readback and configuration/process/package-resolution proof when removing obsolete installations, checkouts and cache snapshots.
- Reconcile mandatory legacy cleanup with MCP process safety by requiring a host-provided Restart/reconnect before deleting any directory still used by an active stdio server.

## [1.9.0] - 2026-08-08

- Require one authoritative Unity compilation after every code or compilation-contract change and treat compile errors as unfinished implementation.
- Separate the mandatory compile gate from Play Mode, tests, builds, runtime, visual and packaged-player validation permissions.
- Reconcile the small-change and packaged-UI rules so they no longer prohibit the mandatory compile gate while retaining all other explicit-authorization boundaries.

## [1.8.0] - 2026-08-05

- Reject symptom-displacement changes such as reordering, priority, threshold, horizon, budget, cadence and failure-boundary movement unless they are proven authoritative product contracts.
- Require complete predecessor/current/successor reachability and transition proofs for planning, publication, pending, adoption, cancellation, revision and deadline state before claiming a root cause.
- Make finite-search ordering a performance concern rather than a correctness owner, and require disproved experimental state and branches to be removed before testing another hypothesis.
- Treat incremental, cross-tick, resumable or asynchronous performance changes as correctness lifecycle changes that must reopen the affected runtime matrix.
- Keep one frozen build across all independent authorized scenarios, record per-scenario failure boundaries and unify attribution before one coherent repair.
- Treat an explicit shared-rule change request as scoped authorization to commit, tag and push the rule repository and migrate only the rule overlays and gitlinks of every consumer project.

## [1.7.0] - 2026-08-04

- Ban fail-close behavior, defensive code, fallback paths, retry-based recovery and guard-driven state during implementation and code review.
- Require contract violations to surface at the originating owner while upstream producers are fixed and downstream defensive branches are removed.
- Delete derivable, duplicated and recovery-only state, and keep each supported Unity version on one compile-time authoritative implementation.
- Remove fallback permissions from Unity MCP, localization preview, package/plugin repair and operational rule wording.
- Replace runtime clamp, `OnValidate` correction and consumer-side serialized-value guards with one authoritative authoring contract.

## [1.6.0] - 2026-08-04

- Prohibit changing the next code layer immediately after observing only the earliest runtime failure boundary.
- Freeze executable implementation and test semantics while completing all independent, authorized scenarios on the same build.
- Require state-transition and evidence-scope proofs across candidates, instances, branches and transactions before one coherent defect-family repair.
- Preserve per-scenario fail-fast safety without allowing global evidence collection to stop or expand into unrelated tests.

## [1.5.0] - 2026-08-04

- Make local UI refresh, visibility, and enablement wiring static-only small changes when they only read existing authoritative state and preserve visible behavior.
- Require explicit, itemized validation authorization in the current request; fixes, implementation, commits, pushes, runtime symptoms, and existing Editor state do not grant it.
- Leave UI behavior that can only be confirmed after Player or content packaging to user validation by default, without Agent compilation, builds, Play Mode substitutes, screenshots, logs, or input automation.
- Prevent consumer-project checklists, read-only MCP tools, and specialized UI workflows from expanding the shared validation boundary.

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
