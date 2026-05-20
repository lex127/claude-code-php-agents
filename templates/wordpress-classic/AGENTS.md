# Classic WordPress Agent Rules

Use this template for non-Bedrock WordPress projects.

## Structure

Common paths:

```text
wp-content/plugins/
wp-content/mu-plugins/
wp-content/themes/
wp-content/uploads/
wp-admin/
wp-includes/
```

## Rules

- Do not edit WordPress core: `wp-admin/` or `wp-includes/`.
- Avoid editing third-party plugins and vendor themes.
- Prefer child themes, custom plugins, or mu-plugins.
- Do not modify `wp-content/uploads/` unless the task is explicitly about media.
- Be careful with cache, rewrite rules, multilingual plugins, and production URLs.

## Commands

Update after verifying locally.

```bash
wp plugin list
wp theme list
wp option get home
```

## Verification

For UI changes, verify in a browser.

For admin changes, do not use real production admin credentials through an agent.
