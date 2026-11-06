# Agent Skills

Practical skills by Vladyslav Prokop for building accessible interfaces and useful interactions across devices.

## Available skills

| Skill | Use it for |
| --- | --- |
| [Accessible UI](skills/accessible-ui/SKILL.md) | Semantic HTML, keyboard interaction, focus, forms, contrast, responsive layout, and accessibility verification. |
| [Interaction Surfaces](skills/interaction-surfaces/SKILL.md) | Apple Live Activities and Dynamic Island, Android Live Updates, phone widgets, Apple Watch, Wear OS, rear displays, and OEM integrations. |

Each skill contains a `SKILL.md` entry point, focused implementation references, and Codex interface metadata. Keep the whole skill directory together: the entry point links to the supporting files.

## Install in Codex

Ask Codex to install either skill from GitHub:

```text
$skill-installer install https://github.com/wladislaw353/agent-skills/tree/main/skills/accessible-ui
```

```text
$skill-installer install https://github.com/wladislaw353/agent-skills/tree/main/skills/interaction-surfaces
```

### Manual installation

Copy the complete directory for each skill you want to use into your personal skills directory:

```text
~/.agents/skills/accessible-ui/
~/.agents/skills/interaction-surfaces/
```

For a project-specific installation, use `.agents/skills/` in that project instead. For example, the entry point should be at `.agents/skills/interaction-surfaces/SKILL.md`.

If an older copy is already installed, update that existing copy rather than creating duplicate skills with the same name. Preserve your own customizations before replacing files. Codex detects skill changes automatically; restart it if the skill does not appear. See the [official local skills documentation](https://learn.chatgpt.com/docs/build-skills).

## Codex plugin package

This repository also includes a portable `plugin.json` that packages both skills, plus a repo marketplace at `.agents/plugins/marketplace.json`. The marketplace points to the repository root. No external services, MCP servers, or credentials are required by this package.

In a supported Codex client, open the cloned repository as a project, restart the client if needed, and choose **Vladyslav Prokop · Agent Skills** in the local plugin sources. Install the `agent-skills` plugin from that source. Follow the [official plugin packaging and local installation guide](https://developers.openai.com/plugins/build/plugins) for client-specific availability.

Use either the plugin or individual skill folders to avoid duplicate installations. A public GitHub repository does not automatically publish the package to the universal plugin directory.

## Use

Invoke a skill explicitly in Codex:

```text
$accessible-ui Build this form with keyboard access, clear errors, and reliable focus behavior.
```

```text
$interaction-surfaces Implement delivery tracking for the existing iOS and Android app. Select useful system surfaces and verify their real availability.
```

Both skills also allow automatic selection when the task matches their descriptions. They guide authorized work within the existing project and platform constraints. They do not install SDKs, register OEM access, or publish an app by themselves.

## Implementation references

Accessible UI includes focused guides for [components](skills/accessible-ui/references/components.md), [visual access](skills/accessible-ui/references/visual.md), and [verification](skills/accessible-ui/references/verification.md).

Interaction Surfaces includes guides for [Apple Live Activities](skills/interaction-surfaces/references/apple-live-activities.md), [Android Live Updates](skills/interaction-surfaces/references/android-live-updates.md), [Apple Watch and Wear OS](skills/interaction-surfaces/references/watches.md), [secondary displays](skills/interaction-surfaces/references/secondary-displays.md), [surface recipes](skills/interaction-surfaces/references/surface-recipes.md), [platform routing](skills/interaction-surfaces/references/platform-routing.md), and [state and action contracts](skills/interaction-surfaces/references/interaction-contract.md).

The references identify concrete APIs and implementation steps. Check current official documentation and the installed SDK before implementation. Hardware support, app eligibility, OS rendering, and physical-device testing remain separate claims.
