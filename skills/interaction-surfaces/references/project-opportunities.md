# Analyze a project and let the person select scenarios

Use for requests such as “what could this product use?”, “analyze all opportunities”, or “suggest scenarios and let me choose”. This is a product discovery mode. Its deliverable is a useful, evidence-based set of choices. Do not implement optional integrations until the person selects them. Explicitly assigned implementations and prior selections remain authorized.

## Inspect the project before proposing a catalogue

Read project guidance and product documentation, inspect existing routes/screens, manifests/targets and dependencies, and trace representative user flows. Identify actual entities, recurring jobs, bounded processes, state changes, command handlers, account model, and available data sources. Inspect rendered UI where available. Read relevant code rather than treating a marketing README as proof of implementation.

For example, a real booking flow may have an upcoming reservation, a time-sensitive arrival, a credential, and cancellation. These are different user jobs; they might support a widget, Live Activity, Wallet pass, or exact-task shortcut. Do not propose them as supported until their data, platform, and integration prerequisites have been checked.

Determine current distribution: web/PWA, native iOS/Android, watch targets, and known audience/device constraints. Separate implemented capability, a technically possible extension, a partner-only integration, and unknown support. Do not invent user behavior, market shares, data availability, signed passes, background services, or native targets.

Inspect only relevant authorized sources. Do not read credentials or unrelated private files to make a review feel comprehensive. If a missing product fact changes the shortlist materially, ask one concise question while continuing independent inspection.

## Sweep the relevant opportunity families

Consider each family for the actual jobs, with a brief reason when it is irrelevant or infeasible:

- In-app status, errors, persistent recovery, contextual help, and support panels.
- Ordinary notifications, conversation replies, media/call integration, and appropriate PiP.
- Eligible Live Activities / Live Updates and meaningful OEM extensions.
- User-configured widgets, Controls, shortcuts, Quick Settings, and capture actions.
- Apple Watch, Wear OS, or another verified watch ecosystem; mirrored versus autonomous work.
- Wallet/pass, arrival, QR presentation, or continuation to another device.
- Rear/cover/matrix displays and supported physical feedback/accessories.
- Adaptive layouts where another window size, fold posture, or input context improves the task.

This is a coverage check, not a requirement to recommend every family or enumerate every API combination. Group redundant ideas by user job. A simple product may have few worthwhile candidates. Keep speculative hardware integrations distinct from scenarios useful to the current audience.

## Make each candidate a concrete choice

Use stable short IDs, such as `S01`, so the person can select without restating the proposal. For each candidate describe:

1. **Job and trigger:** who needs what, when the scenario starts, and the repeated effort it removes.
2. **Experience:** the smallest answer/action and the proposed surface/platform. Include its completion/dismissal condition when relevant.
3. **Project evidence:** the existing flow, route/component, domain data, or integration the proposal relies on. Label a product assumption explicitly.
4. **Readiness and dependency:** works within existing capabilities; needs a specified new target/backend/permission; requires OEM access; or is currently unverified. Cite the relevant current API contract for nontrivial feasibility claims.
5. **Effort and tradeoff:** qualitative small/medium/large only when supported by known work; otherwise say what needs investigation. Include meaningful privacy, battery, maintenance, or duplicate-alert costs. Do not fabricate day estimates, ROI, or audience coverage.
6. **Recommendation and acceptance:** why now/later, and the observable result that would justify the implementation.

Start with a compact table: **ID · scenario and user benefit · surface/platform · readiness/dependency · priority/effort**. Put only decision-critical detail underneath. Distinguish baseline defects from optional enhancements; the person should not need to “opt in” to fixing an already assigned broken form or losing task state.

## Let the person choose

Ask for IDs to implement, defer, or exclude, allowing modifications and “none”. If a question tool is available, use it with a concise self-contained prompt. Keep the catalogue visible in the conversation; a separate document or custom selection UI is unnecessary unless requested.

For example: “Select the scenarios to implement now by ID; mark others as later or excluded. You can also change a proposal.” Do not treat the recommended set, silence, or elapsed time as a selection. Continue independent work that is already authorized, but do not start an unselected extension, widget, native target, or external integration.

After selection, briefly restate the chosen outcomes and dependencies, reuse those decisions, and follow only the relevant implementation references. The selection covers the described implementation scope; it does not silently authorize payments, enrollment, messages, publication, credentials, or other external actions. Resolve an actual missing prerequisite at that boundary without making the person reconfirm ordinary work already chosen.

Remember selected, deferred, rejected, and investigation-needed IDs in the current task context. Do not resurrect excluded ideas on the next screen review. Revisit a decision only when the user asks or materially new evidence changes the benefit or feasibility.
