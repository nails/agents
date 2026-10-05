# Nails

These instructions apply when working in the Nails framework checkout: the directory filled by `nails dev:pull`. They do not apply to a single module cloned on its own.

Edit this file in the `agents` repository. The copies at the checkout root are symlinks created by `dev:pull`.

## Conventions

Keep this file short. Add rules that should be in every chat: PHP, migrations, admin, module layout.

Longer procedures belong in `skills/`, as a directory with a `SKILL.md` (name and description in the frontmatter). Skills load when the task matches; do not put always-on policy in a skill.

Package-specific notes stay in that package. Do not duplicate them here.
