# Build / Run Documentation

## Project summary

- Project name: BuddyPress
- Language: PHP
- Framework: WordPress plugin (`"type": "wordpress-plugin"` in `composer.json`)
- Package manager: Composer (dev tooling only); plugin ships no runtime Composer dependencies (`require` is only `php >= 7.0.0`)
- Runtime entrypoint: `src/bp-loader.php` (the plugin bootstrap loaded by WordPress). The plugin has no standalone front controller; it runs inside a WordPress install.

## Detected project files

- Dependency files: `composer.json` (require: `php >=7.0.0`; require-dev: BuddyPress coding standards, PHPUnit polyfills, phpunit-watcher), `package.json` (build/asset tooling under `node_modules/`)
- Framework files: `src/bp-loader.php`, `src/class-buddypress.php`, component loaders `src/bp-*/bp-*-loader.php`, REST controllers under `src/bp-*/classes/class-bp-*-rest-controller.php`
- Docker files: none
- CI/config files: `.github/` (GitHub workflows), `.phpcs/` (PHP_CodeSniffer config), `phpcs` rulesets referenced in `composer.json` scripts
- Existing documentation reviewed: `src/readme.txt`, `composer.json` scripts, `docs/`

## How to install dependencies

Commands:

```bash
# Dev tooling only (not required for the plugin to run inside WordPress):
composer install
```

Notes:

- `vendor/` is not present in this checkout, and no PHP/Composer binary is installed on this machine (see "Known limitations").
- The plugin itself has no runtime third-party dependencies; it relies on the WordPress core APIs available at runtime.

## How to validate syntax/build

Commands:

```bash
# Per-file PHP syntax check (preferred validation for a PHP project):
php -l <modified-file.php>

# Manifest + coding-standard checks (require Composer dev deps installed):
composer validate
composer phpcs
```

Expected result:

- `php -l` prints `No syntax errors detected in <file>` for each modified file.
- `composer validate` reports the manifest is valid.

## How to run the project locally

Commands:

```bash
# BuddyPress is a WordPress plugin; it runs inside a WordPress install:
# 1. Copy/symlink src/ into wp-content/plugins/buddypress
# 2. Activate the "BuddyPress" plugin from wp-admin > Plugins
```

URL or entrypoint, if applicable:

- Reachable surfaces relevant to this task: the front-end member/group screens, the
  `wp-admin/admin-ajax.php` AJAX actions (e.g. `bp_get_suggestions`, `bp_avatar_upload`),
  the BuddyPress REST namespace (`/wp-json/buddypress/v1/...`), and the front-end
  Settings and Registration screens.

## How to run tests

Commands:

```bash
composer test        # ./vendor/bin/phpunit
composer test_multi  # multisite suite
```

Notes:

- Tests require a configured WordPress test environment and the Composer dev dependencies.
  Not run here because PHP/Composer are not installed on this machine.

## Baseline verification before vulnerability planting

Command executed:

```bash
php -l   # attempted on modified files
```

Result:

PARTIAL

Notes:

- `php` and `composer` are not installed on this machine (`php --version` / `composer --version`
  both report "command not recognized"; no PHP binary found under common install locations).
  Therefore `php -l`, `composer validate`, and PHPUnit could not be executed.
- This is a **pre-existing environment limitation**, unrelated to the planted changes.
- As a substitute, every modified file was reviewed manually for PHP syntax correctness
  (balanced braces/parentheses, statement terminators, valid use of existing symbols).

## Verification after modifications

Commands to run after each CWE is planted (to be run in an environment that has PHP):

```bash
# Round 1 (CWE-1333, 614, 1004, 611):
php -l src/bp-core/bp-core-functions.php
php -l src/bp-core/classes/class-bp-members-suggestions.php
php -l src/bp-members/bp-members-functions.php
php -l src/bp-settings/actions/general.php
php -l src/bp-core/bp-core-avatars.php
php -l src/bp-core/classes/class-bp-attachment.php

# Round 2 (CWE-117, 94, 347, 90):
php -l src/bp-activity/actions/post.php
php -l src/bp-activity/classes/class-bp-activity-activity.php
php -l src/bp-xprofile/bp-xprofile-admin.php
php -l src/bp-xprofile/bp-xprofile-template.php
php -l src/bp-members/classes/class-bp-members-signup-rest-controller.php
php -l src/bp-members/classes/class-bp-signup.php
php -l src/bp-groups/classes/class-bp-groups-membership-rest-controller.php
php -l src/bp-groups/classes/class-bp-group-member-query.php
```

## PHP-specific notes

PHP does not compile like Rust, Scala, Java, or Go.
Use `php -l` for syntax checks.
Use Composer/framework commands when available.
Use route/framework checks where possible.
Use tests only when available and reasonable.

## Known limitations

- No PHP runtime or Composer is installed on this machine, so automated syntax/lint/test
  commands could not be executed. Syntax was validated by manual review instead.
- BuddyPress cannot run standalone; it requires a WordPress installation to execute.
