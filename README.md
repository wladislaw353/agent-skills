# Agent Skills

Practical skills by Vladyslav Prokop for accessible interfaces, adaptive layouts, cross-device interactions, and email development.

## Available skills

| Skill | Use it for |
| --- | --- |
| [Email Development](skills/email-development/SKILL.md) | Transactional and marketing emails, OTP messages and autofill, Gmail structured data, client compatibility, accessibility, and delivery verification. |
| [Accessible UI](skills/accessible-ui/SKILL.md) | Semantic HTML, keyboard interaction, focus, forms, contrast, responsive layout, and accessibility verification. |
| [Adaptive Interfaces](skills/adaptive-interfaces/SKILL.md) | Responsive web/PWA and native app layouts, Flip/Fold postures, window resizing, safe areas, keyboard, text scaling, and task continuity. |
| [Interaction Surfaces](skills/interaction-surfaces/SKILL.md) | Apple Live Activities and Dynamic Island, Android Live Updates, phone widgets, Apple Watch, Wear OS, rear displays, and OEM integrations. |

Each skill contains a shared `SKILL.md` entry point and focused implementation references for Codex and Claude Code. The `agents/openai.yaml` files supply additional Codex interface metadata. Keep the whole skill directory together: the entry point links to the supporting files.

## Install in Codex

Ask Codex to install a skill from GitHub:

```text
$skill-installer install https://github.com/wladislaw353/agent-skills/tree/main/skills/accessible-ui
```

```text
$skill-installer install https://github.com/wladislaw353/agent-skills/tree/main/skills/interaction-surfaces
```

```text
$skill-installer install https://github.com/wladislaw353/agent-skills/tree/main/skills/adaptive-interfaces
```

```text
$skill-installer install https://github.com/wladislaw353/agent-skills/tree/main/skills/email-development
```

### Manual installation

Copy the complete directory for each skill you want to use into your personal skills directory:

```text
~/.agents/skills/email-development/
~/.agents/skills/accessible-ui/
~/.agents/skills/adaptive-interfaces/
~/.agents/skills/interaction-surfaces/
```

For a project-specific installation, use `.agents/skills/` in that project instead. For example, the entry point should be at `.agents/skills/interaction-surfaces/SKILL.md`.

If an older copy is already installed, update that existing copy rather than creating duplicate skills with the same name. Preserve your own customizations before replacing files. Codex detects skill changes automatically; restart it if the skill does not appear. See the [official local skills documentation](https://learn.chatgpt.com/docs/build-skills).

## Install in Claude Code

### Install all four skills as a plugin

Run these commands in your terminal with Claude Code installed:

```sh
claude plugin marketplace add wladislaw353/agent-skills
claude plugin install agent-skills@vladyslav-agent-skills --scope user
claude plugin list
```

This registers the GitHub marketplace and installs the collection for your user account. Start a new Claude Code session after installation if the client requests it. For installation inside an interactive Claude Code session, use:

```text
/plugin marketplace add wladislaw353/agent-skills
/plugin install agent-skills@vladyslav-agent-skills
```

The second slash command opens the plugin details. Choose the install scope in that panel. With the plugin installed, invoke its skills by their namespaced names:

```text
/agent-skills:email-development Create an accessible OTP email with a plain-text alternative and realistic Gmail/autofill support.
/agent-skills:accessible-ui Review this form's semantics, keyboard behavior, errors, and focus.
/agent-skills:adaptive-interfaces Propose useful adaptations for this project's windows and folding devices; let me choose the scenarios.
/agent-skills:interaction-surfaces Analyze this product and propose useful widgets and system surfaces before implementation.
```

To fetch and apply plugin updates, run in your terminal:

```sh
claude plugin marketplace update vladyslav-agent-skills
claude plugin update agent-skills@vladyslav-agent-skills
```

Restart Claude Code to apply an update when required by the client. Installation and updates follow the [official Claude Code plugin guide](https://code.claude.com/docs/en/plugins/install).

### Install one skill manually

Clone this repository and copy the complete `skills/<skill-name>` directory into `~/.claude/skills/`. For example, the installed entry point for Adaptive Interfaces must be `~/.claude/skills/adaptive-interfaces/SKILL.md`, with its `references/` and `agents/` directories alongside it. Keep any existing customizations when updating an older copy.

For project-only discovery, use `.claude/skills/<skill-name>/` in that project instead. A manually installed skill uses its short command, such as `/adaptive-interfaces`, `/interaction-surfaces`, or `/accessible-ui`, without the plugin prefix. Use one installation method for each skill to avoid duplicate copies. Manual copies need to be refreshed when you update the cloned repository; a Git pull alone does not update a separate copy in your skills directory. See [Claude Code skill locations and invocation](https://code.claude.com/docs/en/skills).

## Codex plugin package

This repository also includes a portable `plugin.json` that packages all four skills, plus a repo marketplace at `.agents/plugins/marketplace.json`. The marketplace points to the repository root. No external services, MCP servers, or credentials are required by this package.

In a supported Codex client, open the cloned repository as a project, restart the client if needed, and choose **Vladyslav Prokop · Agent Skills** in the local plugin sources. Install the `agent-skills` plugin from that source. Follow the [official plugin packaging and local installation guide](https://developers.openai.com/plugins/build/plugins) for client-specific availability.

Use either the plugin or individual skill folders to avoid duplicate installations. A public GitHub repository does not automatically publish the package to the universal plugin directory.

## Use in Codex

Invoke a skill explicitly in Codex:

```text
$accessible-ui Build this form with keyboard access, clear errors, and reliable focus behavior.
```

```text
$interaction-surfaces Implement delivery tracking for the existing iOS and Android app. Select useful system surfaces and verify their real availability.
```

```text
$adaptive-interfaces Adapt this website and native app flow to narrow phones, landscape, Fold/Flip, split-screen, keyboard, and large text. Preserve selection and unsaved input.
```

All four skills also allow automatic selection when the task matches their descriptions. They guide authorized work within the existing project and platform constraints. They do not install SDKs, register OEM access, or publish an app by themselves.

## Discover scenarios before implementation

For Claude Code, use the corresponding slash commands listed above in place of the Codex skill prefixes below.

For a broad product review, both Interaction Surfaces and Adaptive Interfaces can inspect the existing project and propose relevant scenarios before changing optional features. Ask, for example:

```text
$interaction-surfaces Analyze this project and propose useful interaction scenarios. Show project evidence, platform readiness, dependencies, priority, and effort. Let me choose what to implement, defer, or exclude.
```

```text
$adaptive-interfaces Review this project's adaptive opportunities across windows, input contexts, and folding devices. Present useful scenarios with IDs so I can select the scope before implementation.
```

The review distinguishes existing capability from new native targets, unverified hardware access, and other dependencies. Recommendations are choices rather than automatic implementation. Explicit implementation requests and previously selected scenarios continue directly; routine fixes do not require a new selection round.

Interaction Surfaces traces proposed data and actions to working project code and requires a concrete benefit over the existing flow. It may recommend no widget or watch integration; a device category is not a reason to invent product features. Adaptive Interfaces uses the current fold geometry: Flip tabletop and landscape Fold tabletop share viewing above and controls below, with bottom sheets contained in and animated from the bottom of the lower region. See [foldable postures and overlays](skills/adaptive-interfaces/references/foldable-postures.md).

## Email development

Use `$email-development` in Codex, `/agent-skills:email-development` with the Claude plugin, or `/email-development` with a manual Claude installation. It reads the existing templates/provider and creates reviewable email content, implementation, and verification. A broad review can propose useful email scenarios for the user to select. Rendering and editing do not authorize sending, DNS changes, or registering a sender.

## Implementation references

Accessible UI includes focused guides for [components](skills/accessible-ui/references/components.md), [visual access](skills/accessible-ui/references/visual.md), and [verification](skills/accessible-ui/references/verification.md).

Adaptive Interfaces includes guides for [web/PWA](skills/adaptive-interfaces/references/web.md), [Android](skills/adaptive-interfaces/references/android.md), [Apple](skills/adaptive-interfaces/references/apple.md), [cross-platform apps](skills/adaptive-interfaces/references/cross-platform.md), and [verification](skills/adaptive-interfaces/references/verification.md).

Interaction Surfaces includes guides for [project opportunity discovery and selection](skills/interaction-surfaces/references/project-opportunities.md), [useful widget creation](skills/interaction-surfaces/references/widgets.md), [Apple Live Activities](skills/interaction-surfaces/references/apple-live-activities.md), [Android Live Updates](skills/interaction-surfaces/references/android-live-updates.md), [Apple Watch and Wear OS](skills/interaction-surfaces/references/watches.md), [secondary displays](skills/interaction-surfaces/references/secondary-displays.md), [surface recipes](skills/interaction-surfaces/references/surface-recipes.md), [platform routing](skills/interaction-surfaces/references/platform-routing.md), and [state and action contracts](skills/interaction-surfaces/references/interaction-contract.md).

The references identify concrete APIs and implementation steps. Check current official documentation and the installed SDK before implementation. Hardware support, app eligibility, OS rendering, and physical-device testing remain separate claims.

Email Development includes [HTML/plain-text implementation and delivery](skills/email-development/references/email-engineering.md), [OTP emails and platform autofill](skills/email-development/references/otp-and-autofill.md), and [Gmail structured data and eligibility](skills/email-development/references/gmail-structured-data.md).
