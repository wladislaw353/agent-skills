# Interaction contract and verification

Use this when a surface carries ongoing state, executes commands, or spans devices. Scale it to the task; a local timer does not need a distributed-event architecture.

## State and ownership

Identify the existing source of truth and separate:

- **Domain state:** Pending, active, paused, awaiting action, completed, canceled, or failed as appropriate to the product.
- **Freshness:** Confirmed/current, stale, or unavailable. Unknown is not zero, empty, complete, or successful.
- **Presentation state:** Requested, eligible, displayed if observable, hidden/dismissed, or ended.
- **Command state:** Requested, pending, confirmed, rejected, or outcome unknown.

Define which component owns each transition. Include identity, revision/order, last-confirmed time, expiry, permitted actions, and public-safe content when useful; do not impose one universal JSON schema.

For multiple surfaces, reuse domain logic and use platform-specific adapters. Do not duplicate an independent order state machine in every widget. Handle delayed/duplicate updates, reconnection, token invalidation, and terminal events so a finished task cannot reappear as active because an older update arrived later.

Make actions idempotent when repetition is possible. Prefer "set paused" over "toggle" for concurrent devices. Recheck identity, authorization, and current state when executing; a deep link or locally visible button is not authority. Do not display success before confirmation unless the product explicitly supports a reversible optimistic state and its recovery behavior is designed.

Dismissal ends or hides the presentation according to platform rules; it does not necessarily cancel the underlying task. End updates when appropriate and retain any needed history in the primary flow. Do not recreate a dismissed activity merely to regain attention.

## Content and freshness

Give each presentation one primary answer. Expanded states may add context and the next useful action. Define zero-data, loading, stale, offline, error, permission-denied, signed-out, completed, and canceled states as relevant.

Use observed phases or measured progress. When there is no trustworthy progress value, use a truthful phase/status rather than invented percentages. A local timer may count toward a known end; a delivery forecast cannot keep pretending to be current after the data expires. Select an explicit stale-state behavior supported by the platform: show its age, hide the uncertain value, or end the live representation.

Check narrow sizes, long translations, large text, reading order, accessible names, contrast, reduced motion, and non-color distinctions. Avoid destructive or sensitive one-tap operations on a public/locked surface. Ensure external controls remain recognizable in dimmed/AOD states and obey platform authentication requirements.

Minimize data on public surfaces and remove account-specific presentations on sign-out/switch. Plan updates and end states without unnecessary polling, wakelocks, vibrations, or duplicate alerts across devices.

## Verification matrix

Select the meaningful cases; record the environment and observed behavior. Use existing project tests where available and follow project rules for retaining or removing scratch tests.

| Condition | Evidence to seek |
| --- | --- |
| Permission denied, revoked, or channel disabled | Honest unavailable state; primary task still accessible |
| Screen locked, AOD, sensitive content hidden | No accidental disclosure; valid authentication flow |
| Background, process killed, reboot | Expected lifecycle and restored state, without stale resurrection |
| Force-stop | Tested separately from ordinary process removal |
| Offline, delayed/out-of-order/duplicate updates | Explicit freshness and correct final state |
| Dismissal and expiry | No unwanted reappearance or endless live representation |
| Multiple activities and accounts | Correct identity, priority, deep link, and cleanup |
| Repeat/concurrent commands | One consistent outcome and useful error handling |
| Disconnected watch or low-power mode | Honest autonomy and synchronization behavior |
| Short viewport, large text, assistive technology | Important information and actions remain reachable |
| Completion/cancellation | Updates stop; retained history and dismissal are intentional |

For native surfaces, note device/model, OS build, region, app version, and relevant settings. Distinguish documentation review, simulator/emulator checks, and physical-device evidence. If hardware is unavailable, test the domain logic and available UI, then report the remaining device validation instead of claiming compatibility.

Verify each exposed action against its real handler and resulting domain state, including a rejected operation. A changed UI label, delivered transport message, or demo response does not prove the app supports the command. Confirm that every displayed metric comes from the identified source and that the implemented surface actually reduces the intended effort. Remove unsupported controls rather than inventing behavior to satisfy the layout.

For in-app panels and overlays, verify real interaction: focus and keyboard access, dismissal, touch, independent scrolling where needed, and access to underlying primary actions. Do not infer this from a screenshot.

## Measuring outcomes

Use task completion, time/steps saved, missed important events, stale-state frequency, action failures, opt-outs, battery/network cost, and support burden. Keep capability, permission, delivery, rendering, engagement, and task completion distinct. Use only observability the platform provides. Compare comparable cohorts; widget adopters may already be more engaged. Do not optimize primarily for opening the app more often when the integration exists to avoid that work.
