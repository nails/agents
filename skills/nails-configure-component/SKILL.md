---
name: nails-configure-component
description: >-
  Adds or changes a Nails component's extra.nails config, services.php Factory
  registrations, Constants::MODULE_SLUG, helpers, or App overrides. Use when
  adding a service, model, resource, factory, or helper, editing composer.json
  extra.nails, or wiring autoload/data for admin or API.
---

# Configure a Nails component

A Nails component is a Composer package with `extra.nails` in `composer.json`. Types: `module`, `driver`, `skin`.

## extra.nails

Module:

```json
"extra": {
    "nails": {
        "moduleName": "cdn",
        "type": "module",
        "namespace": "Nails\\Cdn\\",
        "autoload": {
            "helpers": ["cdn"]
        },
        "data": {
            "nails/module-admin": {
                "autoload": {
                    "assets": {
                        "js": ["admin.min.js"],
                        "css": ["admin.min.css"]
                    }
                }
            }
        }
    }
}
```

`namespace` must match PSR-4 in `autoload` and end with `\\`.

Driver: `type` `driver`, plus `subType`, `forModule` (e.g. `nails/module-invoice`), and `data.class` / `data.namespace` as that module expects.

`extra.nails.autoload` is what Nails boots (helpers, models, services). PHP autoload is still Composer PSR-4.

`extra.nails.data.{other-package}` is config for that package (admin assets, API controller map, CDN image sizes).

## Factory items

Register in `services/services.php`. Keys are what callers pass to Factory. Always allow an app override:

```php
use Nails\Cdn\Service;

return [
    'services' => [
        'Cdn' => function (): Service\Cdn {
            if (class_exists('\App\Cdn\Service\Cdn')) {
                return new \App\Cdn\Service\Cdn();
            }
            return new Service\Cdn();
        },
    ],
    'models' => [ /* same pattern */ ],
    'resources' => [
        'Object' => function ($object): Resource\CdnObject {
            return new Resource\CdnObject($object);
        },
    ],
    'factories' => [ /* new instance each call */ ],
];
```

Load with the package slug, not `'app'`:

```php
use Nails\Cdn\Constants;
use Nails\Factory;

$cdn = Factory::service('Cdn', Constants::MODULE_SLUG);
```

`Constants::MODULE_SLUG` is the Composer name (`nails/module-cdn`). Put it in `src/Constants.php`.

- `Factory::service` / `Factory::model` — one instance
- `Factory::factory` / `Factory::resource` — new instance (`resource` also takes the raw row)

Do not add `nails/agents` as a Composer dependency.
