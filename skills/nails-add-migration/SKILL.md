---
name: nails-add-migration
description: >-
  Adds or changes a Nails database migration in a module, driver, or common.
  Use when creating MigrationN.php, altering schema, making a migration
  Repeatable, running check-migration-numbering, or when the user mentions
  grandfathered migrations, db:migrate, or branch numbering.
---

# Add a Nails migration

`nails make:db:migration` writes into an **app**. In this checkout, add the file in the package.

## Number

List `src/**/Database/Migration/Migration*.php` (common uses `src/Common/Database/Migration`). Use the next unused integer. Never rename, reuse, or insert a number.

If the package has `composer check-migrations`, run it after you write the file. It compares `develop` and `feature/pre-new-admin` and prints the next free number.

Do not add entries to `extra.nails.migrations.grandfathered`. That list only shrinks.

## Class

```php
namespace Nails\{Module}\Database\Migration;

use Nails\Common\Interfaces;
use Nails\Common\Traits;

class Migration12 implements Interfaces\Database\Migration
{
    use Traits\Database\Migration;

    public function execute(): void
    {
        $this->query('ALTER TABLE `{{NAILS_DB_PREFIX}}my_table` ADD `label` VARCHAR(150) NOT NULL DEFAULT "";');
    }
}
```

Nails tables: `{{NAILS_DB_PREFIX}}`. App tables: `{{APP_DB_PREFIX}}`. Prefer `$this->query()` / `$this->prepare()`. Schema helpers: `tableExists`, `columnExists`, `indexExists`, `foreignKeyExists`.

## Two long-lived branches

`db:migrate` stores one integer per component and runs only numbers **above** it.

- Change on **both** branches: number it **at least as high** on the destination as on the source, and guard the DDL (an arriving app may re-run it).
- Change on **this branch only**: implement `Interfaces\Database\Migration\Repeatable` and return immediately when there is nothing to do. Repeatable migrations run on every migrate, forever.

```php
class Migration12 implements Interfaces\Database\Migration\Repeatable
{
    use Traits\Database\Migration;

    public function execute(): void
    {
        if ($this->columnExists('{{NAILS_DB_PREFIX}}my_table', 'label')) {
            return;
        }

        $this->query('ALTER TABLE `{{NAILS_DB_PREFIX}}my_table` ADD `label` VARCHAR(150) NOT NULL DEFAULT "";');
    }
}
```

Permission-string remaps use `Nails\Admin\Traits\Database\Migration\PermissionMap` plus `Repeatable` (see `module-auth` `Migration16`).
