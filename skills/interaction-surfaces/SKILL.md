---
name: interaction-surfaces
description: Analyze product flows and discover, propose, or implement useful interaction surfaces for ongoing tasks, status updates, recurring actions, notifications, and cross-device use. Covers Apple Dynamic Island/Live Activities, Android Live Updates, widgets, watches, secondary displays, OEM SDKs, and in-app feedback. Use for project opportunity reviews before the user chooses scenarios, and proactively during relevant UI work. Exclude purely cosmetic edits, static content, and unrelated backend work.
---

# Interaction Surfaces

Help people understand progress and complete tasks with less effort, inside and outside the main app. Choose surfaces by the user's job, attention, and actual platform access—not by visual novelty or brand exposure.

## Initiative within the assigned task

Choose the working mode from the user's intent. For a broad request to analyze the project, explore possibilities, or let the person decide, use **discovery → scenario selection → implementation**. Read [Project opportunity review](references/project-opportunities.md); inspect the project and present grounded candidates before adding optional features. Wait for the person's choices and implement only the selected scope. Do not mistake a recommendation for a selection.

For an explicit implementation request or scenarios already selected in the conversation, continue that authorized work directly. Do not repeat discovery or ask the user to choose again. Routine bug fixes and accessibility corrections in the assigned flow need not become optional feature proposals.

During relevant UI work, identify worthwhile surface opportunities without waiting for the user to request widgets, notifications, or a dynamic island by name. Inspect the existing flow, framework, design system, platform targets, and available data first. Apply this alongside the project's implementation/design guidance; it does not replace that guidance.

- Implement a clearly useful interaction when it belongs to the assigned flow, uses established capabilities, and is covered by the user's authorization. Do not stop at suggesting work already requested.
- If it requires a new native target, external service, paid dependency, OEM enrollment, or materially different scope, explain the concrete benefit and prerequisite briefly. Continue the authorized work; obtain authorization before the expansion.
- Recommend no additional surface when the benefit is weak. Do not turn every UI task into an ecosystem roadmap or permission questionnaire.
- For recurring glances or commands, evaluate a useful user-configured widget. When it fits the authorized product work, implement its data, configuration, actions, updates, and host integration rather than stopping at a decorative mockup. Use the dedicated widget guide below.
- Never infer permission to send messages, enroll users, purchase services, publish, or change credentials from this skill.

## Select the smallest useful surface

For each plausible candidate, identify the **trigger, user's question, minimum answer, permitted action, and end/dismissal condition**. Prefer a short explanation in the task discussion over a separate planning document.

| User need | Start with | Watch for |
| --- | --- | --- |
| Understand the current interaction | Inline feedback, banner, snackbar | Important errors disappearing before they can be acted on |
| Learn that an event occurred | Standard notification or message history | Unnecessary interruption and duplicates |
| Monitor a bounded, active task | Eligible Live Activity / Live Update | Ineligible promotion, stale status, missing end state |
| Check recurring information | Widget, watch complication, tile | Treating scheduled updates as a live stream |
| Perform a repeated command | Shortcut, Control, Quick Settings tile | Ambiguous state and accidental consequential actions |
| Continue audio, video, or a call | Media/call system integration, suitable PiP | Rebuilding existing system controls as a fake live activity |
| Act while moving | Watch, audio, haptics, permitted vehicle surface | Too much reading, precision input, or device duplication |
| Present a ticket or entitlement | Wallet/pass or the existing ticket flow | A reminder mistaken for a valid credential |
| Show a glance or information to another person | Verified rear/cover screen or hardware SDK | Private panels, wrong touch ownership, public disclosure |
| Continue on another device | Supported handoff/deep link | Opening the home screen instead of restoring task context |

Rank candidates using the frequency and importance of the job, effort saved, reachable audience, attention cost, and maintenance burden. Do not invent user demographics, adoption percentages, or ROI. With missing audience data, state the uncertainty and prefer a narrow, reversible implementation using existing capabilities.

Read only the implementation reference needed for the selected surface:

- [Apple Live Activities](references/apple-live-activities.md): ActivityKit/WidgetKit setup, Dynamic Island layouts, App Intents, APNs tokens/payloads, freshness, and paired-device behavior.
- [Android Live Updates](references/android-live-updates.md): permissions, Kotlin construction, ProgressStyle/MetricStyle, promotion diagnostics, lifecycle, and OEM admission.
- [Watches](references/watches.md): Apple Watch and Wear OS targets, mirrored versus local activities, WidgetKit, Wear Widgets/Tiles, complications, workout APIs, and phone-watch synchronization.
- [Useful widgets](references/widgets.md): select a recurring job; build WidgetKit or Android App Widgets/Glance with configuration, truthful data, actions, sizing, scheduling, and lifecycle checks; distinguish native widgets from web panels.
- [Project opportunity review](references/project-opportunities.md): inspect a product, propose an evidence-based scenario catalogue, explain feasibility, and let the person select what to implement or defer.
- [Secondary displays](references/secondary-displays.md): ordinary app mirroring, Presentation, activity launch, WindowManager, Glyph SDK/Toys, and restricted OEM panels.
- [Other surface recipes](references/surface-recipes.md): phone widgets, Controls, shortcuts, media, Wallet, PWA, support widgets, and physical devices.
- [Platform routing](references/platform-routing.md): use only when the platform/access family remains unclear.
- [Interaction contract](references/interaction-contract.md): state, commands, freshness, and failure testing for live/actionable/cross-device work.

Turn the chosen route into an implementation: identify the owning target/component, SDK symbol and availability, configuration/permissions, source of truth, render/update/end path, action handler, and evidence needed. Use existing architecture and scale this to the task; a concise explanation can replace a separate specification. For new hardware, classify its access model before writing an adapter. Preserve the distinction between documented, approved, compiled, and device-tested support.

For a phone-watch or multi-display flow, read both relevant platform references and the interaction contract. Share domain state and commands, not assumed UI capabilities: a phone notification style may lose fields on the watch, and an exposed display may reject presentation. Compile adapted examples in the actual target before calling them implemented; reference snippets are starting points, not tested project integrations.

## Establish access before promising UI

Keep these claims separate:

1. A feature exists on a device.
2. A documented API supports the intended scenario.
3. This app is eligible and has the necessary access.
4. This device/OS/region/settings combination can display it.
5. The implementation has actually been tested there.

Check current official documentation and the project's installed SDK before choosing APIs, permissions, lifecycle limits, or OEM behavior. Verify exact method availability and release status; do not copy version claims or limits from memory, marketing pages, this skill, or another platform. Cite verified documentation naturally when explaining consequential choices.

Use runtime capability and authorization checks where available. Do not detect support from the brand alone. If documentation or access is missing, name the missing prerequisite, mark the integration unverified, and continue independent in-scope work. Do not invent a working SDK, silently substitute data, or disguise an overlay as native integration.

## Design the interaction, not just the successful frame

Show one primary answer in a compact surface; reveal context and essential actions in expanded states. Adapt content to each surface rather than shrinking the full app. Inspect actual layouts, large text, localization, assistive technology, and light/dark/low-power presentations where relevant.

Distinguish task state, data freshness, presentation visibility, and command outcome. A dismissed card is not a canceled order; an accepted push is not a viewed update; a tapped command is not a completed operation. Use real progress, explicit uncertainty, and confirmed action results.

Honor permissions and dismissal. Ask for access at the moment its benefit is clear. Preserve the existing primary task flow when an optional surface is unavailable. Additional channels or alternative data sources require explicit design and authorization; do not quietly switch from denied push to SMS or a custom overlay.

## Verify proportionally and report evidence

Test the relevant failure states and interaction behavior, not just screenshots. For a web change, use the existing browser workflow; for a native/system surface, browser mockups do not establish device support. Reuse project checks and test only what the change warrants.

In the final result, briefly identify what was implemented, why that surface fits, what was actually tested, and any remaining platform/access dependency. Label illustrations and simulated states as such. Measure task success and effort saved; fewer app opens may be a positive outcome. Do not present provider acceptance as an impression or lack of taps as lack of value.
