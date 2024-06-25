# A vanilla CivicTheme demo on GovCMS SaaS

This is an alternative to [these instructions](https://docs.civictheme.io/development/drupal-theme/using-in-govcms-saas),
with some extra tips about installing demos.

This is valid as of CivicTheme 1.7.1 and I'll update it whenever i work
on a SaaS site that needs CivicTheme.

## Warning

**This is not official documentation.** Consider it like a learning repo. Thanks.

## Install 

This gives you a baseline GovCMS SaaS compatible database. **Do not copy
the composer.json in your SaaS project!** Just the database.

```
git clone git@github.com:simesy/civictheme-saas-starter.git
cd civictheme-saas-starter
ddev start
ddev composer install
ddev import-db --file=baseline-with-blocks.sql.gz
ddev drush cr
ddev drush uli
```

At this point you can move to **Demos**.

### Migrating this to a SaaS project.

So you might have a SaaS project from GovCMS Gitlab, and you want 
to get to started with this database.

1. Copy the `web/themes/custom` into the SaaS project `/themes` folder
2. Build your GovCMS SaaS site with Ahoy
3. Import the database dump `baseline-with-blocks.sql.gz` (ie. using the Ahoy import database command)
4. If desired, export the config (ie. using the Ahoy Drush command)

## Demos

If you've run the above sus

The client may have seen govcms in different setups and expecting these to be replicated.
These are listed at https://www.civictheme.io/civictheme-showcase#sample-template-sites

So these instructions allow you to create the following demos from the civictheme_content
module. Note you have to start with baseline.sql.gz (not the one with blocks).

```
ddev import-db --file=baseline.sql.gz
ddev exec "CIVICTHEME_CONTENT_PROFILE=default drush en civictheme_content -y"

ddev import-db --file=baseline.sql.gz
ddev exec "CIVICTHEME_CONTENT_PROFILE=government drush en civictheme_content -y"

ddev import-db --file=baseline.sql.gz
ddev exec "CIVICTHEME_CONTENT_PROFILE=highereducation drush en civictheme_content -y"

ddev import-db --file=baseline.sql.gz
ddev exec "CIVICTHEME_CONTENT_PROFILE=corporate drush en civictheme_content -y"
```

## Using demos in SaaS

If you want to use any of these on installs onGovCMS SaaS, you must remove
modules not in GovCMS before you export the database.

```
ddev drush pmu civictheme_content default_content -y
```

## How was baseline.sql.gz created?

This is all just my notes for building the sql.gz files.

First update the composer.json to get the right versions you want of GovCMS
and CivicTheme. Then follow these steps which are a basic version of
[these docs](https://docs.civictheme.io/development/drupal-theme/using-in-govcms-saas).

```
# Get a vanilla govcms saas.
ddev composer update
ddev drush si govcms -y

# Install dependencies. This command literally just installs modules.
ddev drush ev "require 'themes/custom/civictheme/theme-settings.provision.inc'; civictheme_enable_modules();"
ddev drush cr

# Enable and set civictheme. Ignore errors about missing blocks.
ddev drush theme:enable -y civictheme
ddev drush config-set -y system.theme default civictheme
ddev drush config-set -y media.settings standalone_url true

# Enable and set adminimal.
ddev drush theme:enable -y adminimal_theme
ddev drush config-set -y system.theme admin adminimal_theme

# Remove govcms content model. Don't forget to uninstall this helper module.
ddev drush en -y civictheme_govcms
ddev drush civictheme_govcms:remove-config --preserve=user_roles
ddev drush pm-uninstall -y civictheme_govcms

# At this point you have a baseline, this is where you need to start for any
# of the content examples below.

# Export the baseline if desired.
ddev drush sql:dump --gzip > baseline.sql.gz
git add . && git ci # etc
```

### Resolving errors

Those instructions above will result in an ultra-minimal setup that is perfect for
installing demos on top of. However for preparing to build a fresh site, these
steps resolve the "This block is broken or missing" errors.

1. log into the site, go to /admin/appearance/settings/civictheme
2. Scroll to CIVICTHEME CONTENT PROVISION, and run that thing. 

```
# Export the baseline-with-blocks if desired.
ddev drush sql:dump --gzip > baseline-with-blocks.sql.gz
git add . && git ci # etc
```