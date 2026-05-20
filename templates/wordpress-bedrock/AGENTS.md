# WordPress Bedrock Agent Rules

This project is a WordPress site built on Bedrock.

## Structure

Common paths:

```text
config/
config/environments/
web/
web/app/plugins/
web/app/mu-plugins/
web/app/themes/
web/wp/
vendor/
```

Theme work should usually go in a child theme or custom theme, not vendor theme files.

## Commands

Replace with the commands used by your project.

```bash
composer install
composer test
composer lint
composer lint:fix
wp plugin list
wp theme list
```

If the project uses Docker, DDEV, or Make targets, document those commands here.

## WordPress Rules

- Do not edit `web/wp/`.
- Do not edit `vendor/`.
- Avoid editing third-party plugins or vendor themes.
- Prefer child themes, custom plugins, or mu-plugins for project-specific code.
- Do not modify `web/app/uploads/` unless the task is explicitly about media.
- Be careful with cache, rewrite rules, multilingual slugs, and production URLs.

## Deployment Rules

The developer owns deploys.

The agent may draft deployment notes, but must not run production commands unless explicitly asked and approved.

## Verification

For browser-facing changes, verify locally with a real browser where possible.

For routing/permalink changes, check:

- expected URL returns 200
- canonical URL
- language variants if multilingual
- cache behavior
