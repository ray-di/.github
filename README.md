# Reusable GitHub Actions Workflows

This repository contains reusable GitHub Actions workflows for PHP projects.

## Workflows

### Continuous Integration (`continuous-integration.yml`)

A reusable workflow for PHP projects that runs PHPUnit tests across multiple PHP versions and dependency combinations.

#### Usage

```yaml
name: CI

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

| Name | Description | Required | Type |
|------|-------------|----------|------|
| `old_stable` | JSON array of PHP versions to test | Yes | string |
| `current_stable` | Current stable PHP version | Yes | string |
| `script` | Additional PHP script to run | No | string |

#### Features

- Runs tests on multiple PHP versions
- Handles both highest and lowest dependencies
- Includes code coverage reporting to Codecov
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

## GH actions workflow templates

* [Github docs about sharing workflows](https://docs.github.com/en/actions/learn-github-actions/sharing-workflows-with-your-organization#using-a-workflow-template)
* [Setup PHP in GitHub Actions](https://bestofphp.com/repo/shivammathur-setup-php-php-miscellaneous).

