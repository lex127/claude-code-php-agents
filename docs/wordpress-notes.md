# WordPress and Bedrock Notes

WordPress projects need stricter boundaries because a repository often mixes custom code, vendor code, uploads, generated assets, and production-like content.

## Prefer Additive Customization

For WordPress work:

- use a child theme instead of editing a vendor theme
- use a small custom plugin instead of patching large third-party plugins
- avoid editing WordPress core
- avoid changing generated uploads unless the task is media-related

## Bedrock Layout

Common Bedrock paths:

```text
config/
web/
web/app/plugins/
web/app/mu-plugins/
web/app/themes/
web/wp/
vendor/
```

Agents should usually avoid:

```text
web/wp/
vendor/
web/app/uploads/
```

unless the task explicitly requires those paths.

## WP-CLI

WP-CLI is powerful and should be treated carefully.

Safe-ish examples:

```bash
wp plugin list
wp theme list
wp option get home
```

High-risk examples requiring human approval:

```bash
wp db import
wp search-replace
wp user update
wp rewrite flush --hard
wp eval-file scripts/some-production-script.php
```

## Caching

WordPress behavior may be affected by:

- page cache
- object cache
- plugin cache
- CDN cache
- browser cache

If a UI or routing change appears wrong, verify whether cache is involved before changing code.

## Multilingual Plugins

For Polylang/WPML-style projects, ask the explorer to check:

- translation relationships
- language-specific slugs
- rewrite rules
- menu translations
- canonical URLs

Do not assume one language represents all languages.
