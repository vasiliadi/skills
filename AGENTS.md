# AGENTS.md

## Adding or changing a skill

- Add new skills to the table in `README.md`, and remove deleted ones.

## Manifests

- Keep the plugin name `vasiliadi-skills` and the marketplace name `vasiliadi` the same in every manifest and in the README install commands.
- Keep `description` the same in all three plugin entries.
- Don't put personal email addresses in manifests. Use the GitHub profile URL as the author contact.

## Releasing

When you release a new version, bump `version` in `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json` (both `metadata.version` and the plugin entry), and `.codex-plugin/plugin.json` together.
