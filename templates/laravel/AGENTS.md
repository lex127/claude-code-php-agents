# Laravel Agent Rules

Use this template for Laravel applications.

## Commands

Update after verifying locally.

```bash
composer install
php artisan test
vendor/bin/pint --test
vendor/bin/pint
php artisan route:list
```

If the project uses Pest, Sail, Docker, DDEV, Vite, npm, or pnpm, document the exact commands here.

## Structure

Common paths:

```text
app/
routes/
database/migrations/
database/factories/
resources/
tests/
config/
```

## Laravel Rules

- Prefer framework conventions.
- Use Form Requests for validation when the project already does.
- Use policies/gates for authorization when relevant.
- Be careful with migrations, queues, notifications, billing, and auth.
- Do not run production migrations.
- Do not read or print `.env` secrets.

## Verification

For backend changes, run relevant tests.

For route/controller changes, inspect routes and add feature tests when possible.

For frontend changes, verify the browser flow locally.
