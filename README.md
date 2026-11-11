Markdown

# Composer Skills Installer Example

This repository demonstrates how to install AI agent skills with Composer using
the [`llm/skills`](https://github.com/roxblnfk/skills) plugin. It is configured
to pull [Drupal AI Best Practices](https://git.drupalcode.org/project/ai_best_practices),
though the same setup works for any repository containing `SKILL.md` files.

## Usage

```bash
composer install
```

The plugin runs automatically after `install` or `update` and:

1. Downloads the `1.0.x` branch of `project/ai_best_practices` from
   `git.drupalcode.org`.
2. Copies all skills from its `skills/` directory into `.agents/skills/`.
3. Symlinks `.claude/skills` to `.agents/skills` so Claude Code discovers them.

Run `composer skills:show` to list installed skills, or `composer skills:update`
to re-sync.

## Configuration

- [`composer.json`](composer.json) requires `llm/skills` and authorizes it in
  `allow-plugins`.
- [`skills.json`](skills.json) configures the plugin:
  - `sources` points to the GitLab repository. Because the project has no tags,
    `ref` pins the `1.0.x` branch.
  - `dependencies.composer: false` disables scanning `vendor/`, ensuring only
    explicitly declared sources are installed.
  - To install specific skills rather than the full repository, add a `skills`
    filter to the source entry (e.g., `"skills": ["drupal-configuration", "drupal-gitlab"]`).

## Adding Other Skills

Any GitHub or GitLab repository containing a `SKILL.md` file can be added as a
source. For example, to install [agent-browser](https://github.com/vercel-labs/agent-browser):

```bash
composer skills:add vercel-labs/agent-browser --skill=agent-browser
```

This appends the source to [`skills.json`](skills.json) pinned to the latest tag
and installs the skill into `.agents/skills/agent-browser/`:

```json
{
    "from": "github",
    "package": "vercel-labs/agent-browser",
    "ref": "^0.42.0",
    "skills": ["agent-browser"]
}
```

You can also edit `skills.json` manually and run `composer skills:update`. Other
supported formats include:

```bash
composer skills:add owner/repo                       # GitHub, latest tag
composer skills:add owner/repo --ref=main            # Branch, tag, or SHA
composer skills:add [https://github.com/owner/repo](https://github.com/owner/repo)    # Full URL
composer skills:add group/project --from=gitlab \
    --host=[https://gitlab.example.com](https://gitlab.example.com)                # Self-hosted GitLab
```

## Why Not `composer require drupal/ai_best_practices`?

Requiring `drupal/ai_best_practices` directly runs its own bundled Composer
plugin, which writes `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, and
`ai_best_practices.yaml` into the project root. Using `llm/skills` avoids root
config clutter and provides a single workflow for installing skills from both
Drupal and non-Drupal repositories.

## License

[MIT](LICENSE)
