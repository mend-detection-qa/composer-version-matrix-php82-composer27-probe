# version-matrix-php82-composer27

## Feature exercised

Toolchain-pin probe that exercises Mend SCA detection of a single-dependency Composer project
resolved under PHP 8.2 and Composer 2.7, verifying that the `plugin-api-version` in the generated
lockfile is `2.6.0` (Composer 2.7 still uses plugin API 2.6.0).

## Pinned toolchain

| Property | Value |
|---|---|
| PHP version | 8.2 |
| Composer version | 2.7 |
| Docker image | `composer:2.7-php8.2` |
| Platform constraint | `php: >=8.2` |
| Expected `plugin-api-version` | `2.6.0` |

## What this probe tests

- Mend reads `composer.lock` (lockfile-driven detection) and resolves both packages.
- `plugin-api-version` in the lockfile must be `2.6.0` — this confirms Composer 2.7 was used.
- Platform constraint `php: >=8.2` appears in `platform` block (not in `packages`).
- Both packages must appear with `source = registry` (Packagist).
- No transitive dependencies may be silently dropped.
- Resolved versions must match the lockfile exactly.

## Expected dependency tree

### Direct dependencies (`require`)

| Package | Version | Source |
|---|---|---|
| `monolog/monolog` | 3.10.0 | registry (Packagist) |

### Transitive dependencies

| Package | Version | Source | Parent |
|---|---|---|---|
| `psr/log` | 3.0.2 | registry | monolog/monolog |

### Summary

- Total packages in lockfile: **2**
- Direct (`require`): **1**
- Transitive: **1**
- Dev packages (`require-dev`): **0**
- All sources: **Packagist registry**

## Mend failure modes exercised

- **Missing transitive dep**: Mend must not silently drop `psr/log 3.0.2`.
- **Wrong resolved version**: Mend must report `3.10.0`, not the `^3.0` constraint range.
- **Source misclassification**: Both packages must be reported as Packagist registry, not VCS or path.
- **Platform constraint bleed**: `php >=8.2` must NOT appear as a detectable package — it is a platform constraint only.

## Expected Mend `.whitesource` versioning context

```json
{
    "php": "8.2",
    "composer": "2.7"
}
```

Note: No `.whitesource` file is committed to this probe repository. The above is the
expected scanning-environment context for CI pipeline configuration.

## Probe metadata

```
pattern:              version-matrix-php82-composer27
pm:                   composer
pm_version_tested:    2.7
php_version_tested:   8.2
docker_image:         composer:2.7-php8.2
plugin_api_version:   2.6.0
lockfile:             composer.lock
total_packages:       2
direct_packages:      1
transitive_packages:  1
generated:            2026-04-29
target:               remote
repo:                 https://github.com/mend-detection-qa/composer-version-matrix-php82-composer27-probe
```