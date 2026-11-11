# Composer Skills Installer Example

This is an example of installing AI Agent SKILLs with Composer through the
[`llm/skills`](https://github.com/roxblnfk/skills) plugin. It installs the
[Drupal AI Best Practices](https://git.drupalcode.org/project/ai_best_practices) SKILLs.
All you need is Composer allow your agent to leverage the SKILLs from AI Best Practices.

## Usage

```bash
composer install
```

The plugin runs after install/update and:

1. Downloads the `1.0.x` branch of `project/ai_best_practices` from *git.drupalcode.org*.
2. Copies every skill under its `skills/` directory into `.agents/skills/`.
3. Links `.claude/skills` to `.agents/skills` so Claude Code finds the same skills.

Run `composer skills:show` to list what is installed, or `composer skills:update` to sync again.

## Configuration

- [`composer.json`](composer.json) requires `llm/skills` and allows it to run as a plugin.
- [`skills.json`](skills.json) is the plugin's config:
  - `sources` points at the GitLab project. The project has no tags, so `ref` pins the branch.
  - `dependencies.composer: false` stops the plugin from scanning `vendor/`, so only the
    declared source is used.
  - To install only some skills, add `"skills": ["drupal-configuration", "drupal-gitlab"]` to
    the source entry.

## Adding Other Skills

Any GitHub or GitLab repository that ships a `SKILL.md` can be added as another source. For
example, to add [agent-browser](https://github.com/vercel-labs/agent-browser):

```bash
composer skills:add vercel-labs/agent-browser --skill=agent-browser
```

This adds an entry to the `sources` list in [`skills.json`](skills.json), pinned to the latest
tag, and copies the skill into `.agents/skills/agent-browser/`:

```json
{
    "from": "github",
    "package": "vercel-labs/agent-browser",
    "ref": "^0.42.0",
    "skills": ["agent-browser"]
}
```

You can also add the entry to `skills.json` yourself and run `composer skills:update`. Here are some more examples:

```bash
composer skills:add owner/repo                       # GitHub, latest tag
composer skills:add owner/repo --ref=main            # branch, tag or commit SHA
composer skills:add https://github.com/owner/repo    # full URL
composer skills:add group/project --from=gitlab \
    --host=https://gitlab.example.com                # self-hosted GitLab
```

## Why Not `composer require drupal/ai_best_practices`?

This example focuses on using `llm/skills`, which allows installation of any skill outside
of the Drupal-verse. That package ships its own Composer plugin, which also writes
`AGENTS.md`, `CLAUDE.md`, `GEMINI.md` and `ai_best_practices.yaml` into the project
root. This project aims to demonstrate how to install any SKILL anywhere on the internet,
beyond what's available in Drupal.

## License

[MIT](LICENSE)