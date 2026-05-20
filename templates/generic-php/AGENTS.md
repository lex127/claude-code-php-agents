# Generic PHP Project Agent Rules

Use this template for custom PHP applications or small framework-light projects.

## Commands

Update these commands after verifying them locally.

```bash
composer install
composer test
composer lint
composer lint:fix
```

## Structure

Document your project structure here:

```text
src/
tests/
public/
config/
```

## Rules

- Keep changes scoped.
- Prefer existing project patterns.
- Do not edit generated files.
- Do not introduce a framework unless explicitly requested.
- Add tests for behavior changes where possible.
- Do not read or print secrets from `.env` files.

## Verification

For each change, report:

- tests run
- lint run
- manual checks
- commands not run and why
