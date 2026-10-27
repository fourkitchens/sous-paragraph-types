# Sous Paragraph Types Drupal Recipe

Provides four reusable paragraph types: Image, Text, Text with Media, and Video.
Their fields have native Drupal authoring widgets and visible media/text
formatters. Text with Media also stores a left/right alignment value for themes;
the default formatter renders its text and media without imposing a layout.

## Supported baseline and dependencies

Use the supported Minimal/PHP baseline in the [platform guide](https://github.com/fourkitchens/sous-drupal-project/blob/main/docs/PLATFORM.md),
with this recipe's Composer dependencies installed. Paragraphs Editor
Enhancements 10.2 requires Drupal 11.4; Drupal 10 and 11.3 are not supported by
this selected dependency set.

Sous Formats and Sous Media are required recipes and are applied automatically.
They provide Basic HTML, image/video Media bundles, and their dependencies.
Sous Content Types and Sous Admin are optional. Paragraphs supplies Entity
Reference Revisions, while Paragraphs Editor Enhancements declares its Paragraphs
Features dependency. Text and Options are installed explicitly for this recipe's
fields and widgets. Focal Point, Media Library Edit, and Field Group are already
declared by Sous Media.

The selected stable module lines are Paragraphs 1.23, Paragraphs Collapsible 1.6,
and Paragraphs Editor Enhancements 10.2. Paragraphs Collapsible is retained for
compatibility. Its maintainer recommends the native Paragraphs widget, and the
[project is no longer developed or covered by Drupal's security advisory policy](https://www.drupal.org/project/paragraphs_collapsible).
The existing Sous Formats/Media `dev-main` requirements remain explicit until
coordinated stable tags actually exist;
global minimum stability is stable. A consuming root project must also explicitly
allow these untagged Sous packages until their first stable tags are published.
`composer install` reproduces the consuming project's lockfile; future major
upgrades require another reviewed dependency update.

This recipe creates paragraph bundles, not a Page host field. Use the public
Sous Paragraphs recipe to attach them to its Page content model, or configure an
Entity Reference Revisions field on another host. The core Body field supplied
by Sous Content Types can coexist with that composition. The starter's
alternative native core Layout Builder option keeps the Body field and supports
per-Page layout overrides. Paragraph Types does not change Layout Builder or
attach a Page host field automatically. The incompatible public
`fourkitchens/sous-layout-builder` package is not used by the starter's offered
native option.

## Install and apply

These examples select the current unreleased implementation on `dev-main`.
Run them in a configured consuming site, not in this recipe source checkout.
[Setup and recipe prerequisites](https://github.com/fourkitchens/sous-drupal-project/blob/main/docs/SETUP.md) define the required
repositories, installer paths, core unpacking plugin, and explicit root stability
exceptions. Use reviewed stable tags once the coordinated releases exist.

In a Sous project:

```sh
ddev composer require fourkitchens/sous-paragraph-types:dev-main
ddev install-recipe fourkitchens/sous-paragraph-types
```

Without the Sous DDEV command, use a consuming project configured as described
in [setup](https://github.com/fourkitchens/sous-drupal-project/blob/main/docs/SETUP.md) and run from its root:

```sh
ddev drush recipe /var/www/html/web/recipes/sous-paragraph-types
ddev drush cache:rebuild
```

Use the [current native unpacking workflow](https://github.com/fourkitchens/sous-drupal-project/blob/main/docs/SETUP.md) before application.

## Portable configuration and matching

Recipe defaults omit exported site UUIDs and settings hashes. The four
`icon_uuid` values and their `file:file:UUID` dependencies are intentionally
retained: each is backed by an embedded PNG in `icon_default`. Paragraphs
natively recreates a missing managed icon file with that UUID and registers its
file usage. Clearing those identifiers would disable the portable icons.

Owned bundles, fields, categories, and form/view displays use strict matching.
Matching configuration is accepted when generated site UUIDs/settings hashes
differ; other owned configuration conflicts are rejected. The shared
`paragraph.preview` view mode is imported from Paragraphs itself. Recipe module
installation suppresses configuration entities, so an explicit import supplies
that native mode on a fresh baseline. It is excluded from the owned strict list
to preserve an already customized shared preview mode.

## Disposable regression check

After applying the recipe, run from a disposable project's root:

```sh
ddev exec env SOUS_DISPOSABLE_TEST=1 vendor/bin/drush --uri=https://PROJECT.ddev.site php:script web/recipes/sous-paragraph-types/tests/verify-paragraph-types.php
```

Replace `https://PROJECT.ddev.site` with the disposable site's actual URL.
The check builds forms for all four types, validates and saves media/paragraphs,
edits and reloads a paragraph revision through its host, and renders text,
images, and the native video iframe. It checks embedded icon bytes, deletes and
recreates the managed icon files through Paragraphs, and verifies file usage.
Video validation uses deterministic native oEmbed cache data rather than relying
on a live provider. This does not test YouTube/Vimeo availability or playback.

With Sous Paragraphs applied, the check uses Page and confirms Body still
renders. Otherwise it creates a temporary host type and field. The normal run
deletes its test content/configuration and restores provider cache data. It is
not safe to run against a production database.

For a retained browser fixture in the disposable site, add
`SOUS_KEEP_TEST_CONTENT=1` after `env` in that command. Pass Drush `--uri` with
the actual DDEV site URL so its absolute oEmbed iframe URLs resolve correctly.
The successful check prints its node path and entity IDs and keeps that content
plus its unique oEmbed cache entries until the disposable installation is removed.

## Development, integration, and releases

This repository is reusable recipe source, not a generated Drupal application.
Its Composer requirements and `recipe.yml` describe installation defaults; the
consuming project owns its runtime, complete configuration export, custom
theme, and application deployment. Requiring `dev-main` downloads remote source;
it does not test uncommitted sibling checkout changes.

Use the shared documentation instead of maintaining another setup procedure:

- [Supported runtime and package relationships](https://github.com/fourkitchens/sous-drupal-project/blob/main/docs/PLATFORM.md)
- [Fresh installation, contributor clones, restoration, and recovery](https://github.com/fourkitchens/sous-drupal-project/blob/main/docs/SETUP.md)
- [Sibling-source development and integration checks](https://github.com/fourkitchens/sous-drupal-project/blob/main/docs/INTEGRATION.md)
- [Configuration ownership and existing-site upgrades](https://github.com/fourkitchens/sous-drupal-project/blob/main/docs/MAINTENANCE.md)
- [Frontend development and production assets](https://github.com/fourkitchens/sous-drupal-project/blob/main/docs/BUILD_TOOLS.md)
- [Production deployment](https://github.com/fourkitchens/sous-drupal-project/blob/main/docs/DEPLOYMENT.md)
- [Coordinated releases and Conventional Commits](https://github.com/fourkitchens/sous-drupal-project/blob/main/docs/RELEASES.md)
- [Maintenance ownership and review cadence](https://github.com/fourkitchens/sous-drupal-project/blob/main/docs/OPERATIONS.md)

Recipe release tooling runs with the Node/npm versions pinned in `.nvmrc` and
`package.json`; Node is not required to execute a recipe in Drupal. The private
npm package publishes no browser assets or npm release. The thin workflow calls
the shared integration gate before the guarded GitHub publisher. Its central
workflow must be rolled out first; source configuration is not proof of a live
GitHub run or release. See the release guide for actual tag state, stable-tag
adoption, and explicit development exceptions.

Package updates do not automatically change configuration already applied to a
site. Preserve its complete export and adopt changes through reviewed native
configuration migrations and project-owned overrides. Do not routinely replay
the entire recipe on a customized site.

## License

Sous recipe source is licensed under [GPL-2.0-or-later](LICENSE). Dependencies
retain their own license terms; see the shared license policy in the platform
guide.
