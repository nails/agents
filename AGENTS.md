# Nails

These instructions apply when working in the Nails framework checkout: the directory filled by `nails dev:pull`. They do not apply to a single module cloned on its own.

Edit this file in the `agents` repository. The copies at the checkout root are symlinks created by `dev:pull`.

## Workspace

Each directory is its own git repo (`common`, `module-cdn`, `driver-invoice-stripe`, …). Edit and commit in the package you changed. Do not invent a root `composer.json`.

Prefer `develop`. Some packages are still on `feature/pre-new-admin`; stay on that package's current branch unless the task is to move it.

## PHP

PHP 8.3+. Follow the surrounding file for formatting. Hungarian prefixes (`$o`, `$a`, `$s`, `$b`, `$i`) are deprecated: new code uses declared types and ordinary names (`User $user`, `array $rows`). Do not rename Hungarian in files you are not otherwise changing.

Resolve services, models, factories, and resources through `\Nails\Factory`, with the package's `Constants::MODULE_SLUG` as the provider.

In PHP views, use curly braces (`if { }`, `foreach { }`). Do not use alternative syntax (`if (): endif;`, `foreach (): endforeach;`).

## Migrations

Never renumber or reuse a migration. The next class is `MigrationN` at the next unused integer. Schema changes that must survive a branch switch are `Repeatable` and cheap-guard first (`tableExists`, `columnExists`). Do not add to `extra.nails.migrations.grandfathered`.

Use the **nails-add-migration** skill when adding or changing a migration.

## Admin

New admin UI lives in `src/Admin/Controller` (`Nails\{Module}\Admin\Controller`), extending `Nails\Admin\Controller\DefaultController` (CRUD) or `Base` (custom). Permissions are classes in `src/Admin/Permission`. Do not add controllers under `admin/controllers` or `App\Admin\App`.

Admin controllers render pages and handle form posts. Do not squeeze JSON, AJAX, or other API-like endpoints into them.

Use the **nails-add-admin-controller** skill when adding or changing an admin page.

## API

JSON, AJAX, and other HTTP-API behaviour belongs in `src/Api/Controller` (`Nails\{Module}\Api\Controller`), extending `Nails\Api\Controller\Base` or `CrudController`. Admin-only endpoints use `Nails\Admin\Traits\Api\RestrictToAdmin`.

## Components

A Nails package is a Composer package whose `composer.json` has `extra.nails` (`type`, `namespace`, and for modules `moduleName`). Register Factory items in `services/services.php`. Allow `App\{Module}\…` overrides.

Use the **nails-configure-component** skill when adding a service, model, helper, or changing `extra.nails`.

## Skills

Longer procedures live in `skills/<name>/SKILL.md`. Load them when the task matches; do not put always-on policy in a skill. Package-specific notes stay in that package.
