# Analyze a project and let the person select scenarios

Use for requests such as “what could this product use?”, “analyze all opportunities”, or “suggest scenarios and let me choose”. This is a product discovery mode. Its deliverable is a useful, evidence-based set of choices. Do not implement optional integrations until the person selects them. Explicitly assigned implementations and prior selections remain authorized.

## Inspect the project before proposing a catalogue

Read project guidance and product documentation, inspect existing routes/screens, manifests/targets and dependencies, and trace representative user flows. Identify actual entities, recurring jobs, bounded processes, state changes, command handlers, account model, and available data sources. Inspect rendered UI where available. Read relevant code rather than treating a marketing README as proof of implementation.

For each shortlisted job, trace the actual data read path and any command from entry point through handler to resulting state, including persistence/API where applicable. Note file/symbol evidence and missing links. Check whether the action is really available to this user in this state. A route name, entity type, disabled button, mock, or TODO does not establish a functioning capability. A booking record, for example, proves neither cancellation nor an issued credential.

Determine current distribution: web/PWA, native iOS/Android, watch targets, and known audience/device constraints. Separate implemented capability, a technically possible extension, a partner-only integration, and unknown support. Do not invent user behavior, market shares, data availability, signed passes, background services, or native targets.

Inspect only relevant authorized sources. Do not read credentials or unrelated private files to make a review feel comprehensive. If a missing product fact changes the shortlist materially, ask one concise question while continuing independent inspection.

## Match evidenced jobs to relevant surfaces

Use these families as a lookup for jobs found in the code, not as a list to populate:

- In-app status, errors, persistent recovery, contextual help, and support panels.
- Ordinary notifications, conversation replies, media/call integration, and appropriate PiP.
- Eligible Live Activities / Live Updates and meaningful OEM extensions.
- User-configured widgets, Controls, shortcuts, Quick Settings, and capture actions.
- Apple Watch, Wear OS, or another verified watch ecosystem; mirrored versus autonomous work.
- Wallet/pass, arrival, QR presentation, or continuation to another device.
- Rear/cover/matrix displays and supported physical feedback/accessories.
- Adaptive layouts where another window size, fold posture, or input context improves the task.

Apply two checks before shortlisting: **does the product actually support this information/action, and does this surface make that job materially easier?** Group redundant ideas by user job. Omit weak or unsupported candidates rather than padding the catalogue; a brief reason is enough for a surface the user specifically asked about. An empty shortlist is a valid outcome. Do not turn missing domain behavior into a roadmap unless the user asks to explore new product capabilities. Separate that exploration from adapting existing features.

## Make each candidate a concrete choice

Use stable short IDs, such as `S01`, so the person can select without restating the proposal. For each candidate describe:

1. **Job and trigger:** who needs what, when the scenario starts, and the repeated effort it removes.
2. **Experience:** the smallest answer/action and the proposed surface/platform. Include its completion/dismissal condition when relevant.
3. **Project evidence:** file/symbols for the data read path and each proposed command's handler/result; state which parts were inspected or exercised. Show the existing flow being improved and the concrete steps/effort avoided. Do not present an assumption as implemented functionality.
4. **Readiness and dependency:** distinguish domain readiness from surface readiness. A missing watch target can be a presentation dependency; a missing cancellation handler is a new product capability, not watch integration plumbing. Name missing targets/permissions/OEM access and unverified behavior. Cite the relevant current API contract for nontrivial feasibility claims.
5. **Effort and tradeoff:** qualitative small/medium/large only when supported by known work; otherwise say what needs investigation. Include meaningful privacy, battery, maintenance, or duplicate-alert costs. Do not fabricate day estimates, ROI, or audience coverage.
6. **Recommendation and acceptance:** why now/later, and the observable result that would justify the implementation.

Start with a compact table: **ID · scenario and user benefit · surface/platform · readiness/dependency · priority/effort**. Put only decision-critical detail underneath. Distinguish baseline defects from optional enhancements; the person should not need to “opt in” to fixing an already assigned broken form or losing task state.

## Let the person choose

When useful candidates exist, ask for IDs to implement, defer, or exclude, allowing modifications and “none”. If none pass, report that outcome and the evidence briefly; do not ask the person to select from an empty catalogue. If a question tool is available, use it with a concise self-contained prompt. Keep the catalogue visible in the conversation; a separate document or custom selection UI is unnecessary unless requested.

For example: “Select the scenarios to implement now by ID; mark others as later or excluded. You can also change a proposal.” Do not treat the recommended set, silence, or elapsed time as a selection. Continue independent work that is already authorized, but do not start an unselected extension, widget, native target, or external integration.

After selection, briefly restate the chosen outcomes and dependencies, reuse those decisions, and follow only the relevant implementation references. The selection covers the described implementation scope; it does not silently authorize payments, enrollment, messages, publication, credentials, or other external actions. Resolve an actual missing prerequisite at that boundary without making the person reconfirm ordinary work already chosen.

Remember selected, deferred, rejected, and investigation-needed IDs in the current task context. Do not resurrect excluded ideas on the next screen review. Revisit a decision only when the user asks or materially new evidence changes the benefit or feasibility.
