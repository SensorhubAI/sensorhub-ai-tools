# Sensorhub AI tools

This repo is public. Everything in it, including a branch before it's merged, can be read by anyone.

## What belongs here

Free tools for people outside Sensorhub: Claude skills and the templates, references and example files they need.

## Rules

- Never copy files from a private Sensorhub repository. Write or rewrite each piece for the public.
- Before every push, check for keys and tokens, project IDs, customer names or data, internal links and personal details. Push only after that check.
- Every figure a tool reports must come from the user's own Sensorhub data or a source the report names.
- Call them free tools or AI tools.
- Use single dashes, never em dashes.

## Layout

- Each tool is its own plugin: `plugins/<tool>/.claude-plugin/plugin.json` and `plugins/<tool>/skills/<tool>/SKILL.md`.
- List every plugin in `.claude-plugin/marketplace.json`, and add it to the README table, install commands and download links.
- Run `claude plugin validate .` and `claude plugin validate plugins/<tool>` before opening a pull request.

## Releasing

Changes go through a pull request. After it's merged, publish a GitHub release with one `<tool>.skill` file per tool. Build each file from `main` as a zip whose top folder is `<tool>/`, so the README's `releases/latest/download/<tool>.skill` links keep working.
