---
name: nails-add-admin-controller
description: >-
  Adds or changes a Nails admin controller, permission class, or admin view.
  Use when building an admin page, CRUD for a model, sidebar nav, DefaultController
  constants, userHasPermission, or when the user mentions Admin\Controller,
  announce(), or the old admin/controllers path.
---

# Add an admin controller

Admin discovers instantiable classes in the component's `Admin\Controller` namespace that implement `Nails\Admin\Interfaces\Controller`. In a module that is `src/Admin/Controller/` → `Nails\{Module}\Admin\Controller`.

Do not add files under `admin/controllers` or namespaces like `Nails\Admin\{Module}` / `App\Admin\App`. Those are the old stack. Some packages still have them; new work uses `src/Admin/Controller`.

## Which base class

- **CRUD for a model:** `Nails\Admin\Controller\DefaultController`. Set `CONFIG_MODEL_NAME` and `CONFIG_MODEL_PROVIDER` (`Constants::MODULE_SLUG`). Set `CONFIG_PERMISSION_*` to permission class names.
- **Custom screens:** `Nails\Admin\Controller\Base`. Implement `announce()` and the action methods.

`nails make:controller:admin` is for apps (`App\Admin\Controller`). In a module, write the class yourself.

## Permissions

One class per action under `src/Admin/Permission/`, implementing `Nails\Admin\Interfaces\Permission`:

```php
namespace Nails\Auth\Admin\Permission\Users;

use Nails\Admin\Interfaces\Permission;

class Browse implements Permission
{
    public function label(): string
    {
        return 'Can browse users';
    }

    public function group(): string
    {
        return 'User Accounts';
    }
}
```

Identity is the FQCN. Check with `userHasPermission(Permission\Users\Browse::class)` in `announce()` (sidebar) **and** in the action (typed URLs). Empty permission values pass. Super users pass everything.

## Sidebar

```php
public static function announce(): Nav|array|null
{
    $nav = Factory::factory('Nav', \Nails\Admin\Constants::MODULE_SLUG);
    $nav
        ->setLabel('Users')
        ->setIcon('fa-users');

    if (userHasPermission(Permission\Users\Browse::class)) {
        $nav->addAction('Manage Users');
    }

    return $nav;
}
```

Groups merge by label. Only add actions the current user can use; return `null` to stay routable with no sidebar entry. Use `static::url('edit/' . $id)` instead of hardcoding `/admin/...`.

## Views

PHP templates, not under `src/`. A module looks in `admin/views/{Controller}/`, directory name matching the class name exactly. `$this->loadView('index')` wraps header/footer. DefaultController supplies index/edit/order unless you override them.

URL: `/admin/{module-slug}/{controller}/{method}/…` where `{controller}` is the class name relative to `Admin\Controller`, dash-cased.
