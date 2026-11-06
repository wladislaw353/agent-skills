# Component behavior

Use this reference for interactive components. Select the relevant pattern from the [WAI-ARIA Authoring Practices Guide](https://www.w3.org/WAI/ARIA/apg/patterns/), then test the actual implementation. APG examples are illustrative, not universal browser/assistive-technology compatibility guarantees.

## Semantics and navigation

- Preserve meaningful heading hierarchy and page regions. Give repeated landmarks distinct names where needed. Use a skip link when repeated navigation obstructs access to main content.
- Use anchors with real destinations for navigation, including same-page links. Use buttons for actions, with type="button" unless they intentionally submit a form.
- Do not assign application-menu roles to ordinary site navigation. A disclosure containing links is often sufficient; an ARIA menu requires its own keyboard model.
- Keep DOM and reading order consistent with the meaningful visual order. CSS reordering must not create a contradictory journey.
- For client-side navigation, update the document title and provide a predictable focus/announcement strategy using framework conventions. Do not move focus for every reactive update.
- Icon-only controls need meaningful names. Prefer visible labels when practical. Decorative icons should not duplicate the name; informative images need contextual alternatives, decorative images alt="". Complex charts need a textual explanation and accessible data.

## Forms and errors

- Associate a persistent label with each field. Placeholders are hints, not labels. Use fieldset/legend for related choices when appropriate; use suitable input types and autocomplete tokens.
- Explain required formats before submission. Identify required fields in text and programmatically. Do not rely on color or an unexplained asterisk.
- Preserve entered data after errors. Associate actionable error messages with the affected fields; set aria-invalid for actual validation failures and clear it after correction. Keep described-by references valid.
- Choose an intentional error-summary/focus strategy for failed submission. Avoid simultaneously moving focus and firing multiple assertive announcements of the same message.
- Announce relevant asynchronous results without stealing focus. Use an existing polite status region for routine updates; reserve alerts for urgent information. Announcing every keystroke or replacing a live region wholesale can create noise or missed messages.
- Loading, disabled, empty-result, and success states need names and behavior too. Prevent duplicate submissions without losing focus or removing the only explanation. aria-disabled alone does not prevent activation; native disabled controls leave the tab order.
- Allow paste, password managers, and autofill. Test whole-code pasting into one-time-code UIs. Do not make memory puzzles the only authentication route; examine WCAG 3.3.8 conditions and exceptions for the actual flow. Avoid requesting the same information repeatedly within a process when it can be reused.

## Selects and composite widgets

Prefer native select for straightforward selection. Use a custom widget when the task needs it, not merely because the native arrow differs from the mockup.

For a custom control, define:

- Pattern: select-only combobox, editable combobox, listbox, tabs, tree, grid, or another specific interaction.
- Name, role, value, expanded/selected/disabled states, and relationship to the popup or controlled content.
- Keyboard contract: Tab entry/exit, arrows, activation, Escape, and typeahead/Home/End where appropriate. Selection and the active option are not necessarily the same state.
- One coherent focus model: roving tabindex or focus retained with valid aria-activedescendant, according to the pattern. Keep active options visible; virtualized options must exist when referenced.
- Form contract: one source of truth, submitted value, reset, required/disabled state, and validation.
- Pointer, touch, screen-reader, and mixed-input behavior, including empty and loading states.

A hidden native field does not make a custom front end accessible. Do not present two conflicting accessible controls for the same choice. Avoid putting aria-hidden on keyboard-reachable elements.

## Dialogs, popovers, and disclosures

- Choose modality based on the task. Prefer native dialog behavior or an established primitive where suitable; verify its labeling, initial focus, closing, and restoration in context.
- A modal dialog needs an accessible name, suitable initial focus, contained Tab navigation, noninteractive background, and an accessible exit. aria-modal alone provides none of that behavior.
- Restore focus to the trigger or a logical successor if it no longer exists. Do not always focus the first input: lengthy introductory content may need to be encountered first.
- Nonmodal popovers and disclosures generally must not trap focus or make the rest of the page inert. Synchronize expanded state with visibility.
- Keep content available without hover alone. Custom hover/focus content needs a usable dismissal path and must remain available while the user interacts with it, subject to the applicable WCAG requirements. Essential instructions should not live only in a tooltip.
