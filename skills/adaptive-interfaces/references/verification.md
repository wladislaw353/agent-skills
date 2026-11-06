# Verify adaptive behavior through real tasks

Use the project's current testing workflow. Keep scratch artifacts outside the repository unless they are requested deliverables. Test the assigned flow proportionally; do not make every layout fix a device lab project. Report untested environments plainly.

## Build a focused matrix

Start with audience evidence and supported targets. If unavailable, use representative constraints rather than pretending every phone is covered. For web, useful initial sizes include narrow 320–390 CSS px, common phone widths, short landscape, a tablet-width window, and resizable desktop. These are probes, not production breakpoints. Test immediately on both sides of each actual threshold and sweep intermediate widths.

| Situation | Behavior to verify |
| --- | --- |
| Narrow window / long translation | Primary task, labels, and actions fit without hidden document overflow |
| Wide but short / keyboard visible | Focused field, error, submit/cancel, and dismissal remain reachable |
| Large text / browser zoom | Reflow and readable hierarchy without disabling user preferences |
| Expanded width | Extra pane improves the task; line lengths and forms remain sensible |
| Compact → expanded → compact | Selection, draft, errors, scroll anchor, Back path, and session survive |
| Half-open / separating hinge | Intended pane placement; no key content or interaction over occlusion |
| Outer → inner → outer display | Permitted launch, state continuity, density/inset changes, correct task destination |
| Split-screen / desktop resize | Continuous adaptation and input access without restarting the operation |
| RTL / screen reader / keyboard | Logical order, focus visibility, meaningful names, and all actions reachable |
| Process recreation / restoration | Durable task data restores; consequential operations do not run twice |

For Android, vary display size/font scale, system navigation, target window classes, posture, and multi-window. For Apple, vary supported scenes, Dynamic Type, orientation, safe areas, and multitasking. For browser work, include the actual mobile Safari/Chrome or embedded browser when those are part of the target.

## Browser procedure

1. Open the actual affected route and exercise the primary task at a normal width.
2. Resize across thresholds and inspect page geometry. Compare `document.documentElement.scrollWidth` with the layout viewport to detect unexpected document overflow, then inspect intentionally scrolling regions separately. A numerical match alone does not prove controls are visible or usable.
3. Open menus/dialogs, enter long content, submit invalid data, and verify error recovery. Test page and nested scroll independently; check sticky elements against the available height.
4. Verify keyboard focus and dismissal at each relevant layout. A hidden pane must not retain interactive descendants in the tab sequence. Keep focus on the logical object/action through restructuring.
5. Test zoom/text scaling and real on-screen keyboard behavior. Desktop viewport emulation does not reproduce browser chrome, the real IME, device pixel ratio, or a folding hinge.
6. Capture a few representative stable screenshots plus observed interaction results. Wait for transitions to settle before judging geometry; do not disable animations in the product simply to obtain a screenshot.

Do not add a universal `scrollWidth` assertion that rejects intentional 2D content without inspecting its container. Do not take a screenshot at one marketing device size and call the whole responsive experience tested.

## Native continuity procedure

Open a specific detail item, type an unsaved draft, set a nondefault scroll position, and trigger a reversible pending action if the flow has one. Fold/unfold, resize, rotate, show/hide the keyboard, then return to compact. Confirm the same object, correct Back semantics, draft/errors, and one consistent action result. Repeat with recreation/termination only where the task and supported lifecycle warrant it.

Use emulator posture controls for a configured foldable and the project's UI tests for stateful behavior. Use physical hardware to establish hinge reporting, cover-display eligibility, touch handoff, device-specific keyboard/insets, and continuity where OEM behavior matters. Do not silently force display IDs, grant special permissions, or change unrelated device settings to make a demonstration pass.

## Useful outcomes and evidence

Report the environment, window/content constraints, actual task performed, observed result, and remaining unknowns. Include failures as concrete bugs, such as “submit action is below the visible region when the last field is focused,” rather than “mobile looks bad.” Measure successful completion, reachability, lost-state incidents, and effort saved. Avoid a compatibility percentage or “all devices supported” claim without a defensible device population and test method.
