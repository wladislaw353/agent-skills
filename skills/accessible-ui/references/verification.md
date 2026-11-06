# Verification proportional to the change

Verify observable behavior, not the number of ARIA attributes or a score alone. [WAI's tool-selection guidance](https://www.w3.org/WAI/test-evaluate/tools/selecting/) explains why automated evaluation needs human judgment.

## Choose a useful boundary

- Small semantic or copy fix: inspect the resulting DOM/accessibility information and exercise the affected interaction.
- New or changed interactive component: verify its meaningful states and transitions, keyboard behavior, labels, focus, and relevant viewport/preferences. Add a behavioral regression where the project and task warrant one.
- New page or layout/theme change: also check reflow, resizing, contrast, long content, and boundaries between sections. A sticky header, overlay, or automatic theme transition must not hide controls or put light text on a light footer.
- Requested audit: define scope, target version/level, representative templates, complete processes, and environments. Distinguish sampled checks from a whole-product conformance claim.

Use available project tooling. Do not install a new testing stack, contact participants, submit real transactions, or interact with production records merely because this skill is active. Use authorized fixtures and local/test environments; report a missing capability instead of claiming it ran.

## Exercise the user journey

1. Start from the entry point, not a pre-focused control. Find and understand the action.
2. Complete it without a mouse. Verify forward/backward Tab order, activation and pattern-specific keys, visible focus, and absence of accidental traps.
3. Open and close overlays. Check initial focus, background interaction, Escape where appropriate, and focus restoration.
4. Trigger validation, loading, empty results, cancellation, and success where applicable. Check preserved input, error associations, announcements, and duplicate-action handling.
5. Check relevant pointer/touch alternatives separately. Do not infer screen-reader behavior from keyboard success.

## Inspect and measure

- Inspect computed roles, names, values, states, and relationships in the accessibility tree. This is useful evidence, not a substitute for operating a screen reader.
- Run the project's automated accessibility checks against interactive states, not only initial HTML. A clean axe, WAVE, or Lighthouse result does not establish conformance. WAVE is not a screen-reader simulator.
- For visual changes, measure real color pairs and check zoom, reflow, text spacing, forced colors, and reduced motion as relevant. A screenshot alone does not measure contrast or prove keyboard access.
- For screen-reader-sensitive changes, test suitable browser/screen-reader combinations when available. Check navigation by headings/regions, form names and errors, dynamic messages, and mobile gestures if relevant. State the combination and version used; do not generalize one result to all assistive technology.
- User evaluation complements criteria testing. Recommend it for consequential or unfamiliar flows; do not fabricate participants, findings, or certification.

## Regressions that earn their place

Prefer tests such as: submit invalid data, follow an error to its field, correct it, then receive confirmation; open a dialog, navigate inside it, close it, and resume at the trigger; operate a selection widget and verify the value actually submitted.

Avoid tests that only assert a particular class, copied ARIA literals, or source-code strings. Use computed semantics and visible behavior. Keep or remove tests according to the user's/project's test-hygiene rules. Clean up temporary artifacts and sessions you created; preserve existing ones.

## Report what the evidence supports

A useful finding includes the task/state, environment, reproduction, observed behavior, user impact, expected behavior, relevant criterion when established, and retest result. Prioritize blocked essential tasks and widely reused defects without pretending that priority waives a conformance requirement.

For normal implementation, report only the relevant checks and unresolved limitations. If no screen reader was available, say so when that materially limits verification. Never claim “100% accessible,” certification, or a legal conclusion based on partial tests. Standards and laws are distinct; check the actual jurisdiction and requirement if legal compliance is part of the request.
