# Reusable GitHub Actions Workflows

This repository contains reusable GitHub Actions workflows for PHP projects.

## Workflows

### Continuous Integration (`continuous-integration.yml`)

A reusable workflow for PHP projects that runs PHPUnit tests across multiple PHP versions and dependency combinations.

#### Usage

```yaml
name: Continuous Integration

on:
  push:
  pull_request:
  workflow_dispatch:

jobs:
  tests:
    uses: ray-di/.github/.github/workflows/continuous-integration.yml@v1
    with:
      old_stable: '["8.1", "8.2", "8.3"]'
      current_stable: "8.4"
```

#### Inputs

| Name | Description | Required | Type | Default |
|------|-------------|----------|------|---------|
| `old_stable` | JSON array of older PHP versions to test, e.g. `'["8.2", "8.3"]'` | No | string | `"[]"` |
| `current_stable` | Current stable PHP version | Yes | string | |
| `script` | Run as `php <script>` after the tests, e.g. `demo/run.php` | No | string | |

#### Features

- Runs tests on multiple PHP versions
- Handles both highest and lowest dependencies
- Uploads coverage to Codecov; `secrets.CODECOV_TOKEN` is used only when the caller passes `secrets: inherit` (or `CODECOV_TOKEN`), otherwise the upload is tokenless. A failed upload does not fail the job
- Caches Composer dependencies
- Tests on both Ubuntu and Windows (for current stable PHP version)
- Configurable additional script execution

#### Test Matrix

The workflow runs tests with the following combinations:

- All PHP versions from `old_stable` on Ubuntu with both highest and lowest dependencies
- Current stable PHP version on:
    - Ubuntu (highest dependencies)
    - Ubuntu (lowest dependencies)
    - Windows (highest dependencies)

### Static Analysis (`static-analysis.yml`)

Runs PHPStan, Psalm, PHPMD and ComposerRequireChecker as separate jobs on one PHP version.

#### Usage

```yaml
jobs:
  sa:
    uses: ray-di/.github/.github/workflows/static-analysis.yml@v1
    with:
      php_version: "8.4"
```

#### Inputs

| Name | Description | Required | Type | Default |
|------|-------------|----------|------|---------|
| `php_version` | PHP version used by every job | Yes | string | |
| `has_crc_config` | Pass `--config-file=./composer-require-checker.json` to ComposerRequireChecker | No | boolean | `false` |

#### Commands

The project's own `require-dev` tools are used:

- PHPStan: `./vendor/bin/phpstan analyse -c phpstan.neon`
- Psalm: `./vendor/bin/psalm --show-info=false --shepherd`
- PHPMD: `./vendor/bin/phpmd src text ./phpmd.xml` (the old positional form, which PHPMD 3 still runs but reports as deprecated; the project needs a `phpmd.xml`)
- ComposerRequireChecker: installed with `composer global require maglnet/composer-require-checker`, then `composer-require-checker check ./composer.json`

`static-analysis-no-phpmd.yml` takes the same inputs, skips the PHPMD job and runs ComposerRequireChecker from the latest release PHAR.

### Coding Standards (`coding-standards.yml`)

Runs `composer validate --strict` and `./vendor/bin/phpcs src tests`.

#### Usage

```yaml
jobs:
  cs:
    uses: ray-di/.github/.github/workflows/coding-standards.yml@v1
    with:
      php_version: "8.4"
```

#### Inputs

| Name | Description | Required | Type | Default |
|------|-------------|----------|------|---------|
| `php_version` | PHP version used to run the checks | Yes | string | |

## GH actions workflow templates

* [Github docs about sharing workflows](https://docs.github.com/en/actions/learn-github-actions/sharing-workflows-with-your-organization#using-a-workflow-template)
* [Setup PHP in GitHub Actions](https://bestofphp.com/repo/shivammathur-setup-php-php-miscellaneous).

