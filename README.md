# A vanilla CivicTheme demo on GovCMS SaaS

This just gives you a vanilla GovCMS with Civictheme that you would get if you followed
[these instructions](https://docs.civictheme.io/development/drupal-theme/using-in-govcms-saas).

This is valid as of CivicTheme 1.7.1 and I'll update it whenever i work
on a SaaS site that needs CivicTheme.

## Warning

This is not official documentation. Consider it like a learning repo. Thanks.

## Install

If you want to see what a basic civictheme looks like, so many you 
want to check out the blocks etc. Something like:

```
git clone git@github.com:simesy/civictheme-saas-starter.git
cd civictheme-vanilla
ddev start
ddev composer install
ddev import-db --file=baseline.sql.gz
ddev drush cr
ddev drush uli
```

### Initialising your saas/civictheme site

You *don't* need to build the site, just copy the custom themes into your
SaaS project, and then import the database. So:

1. Copy the web/themes/custom into the saas /themes folder
2. Build your govcms site with Ahoy
3. Import the database dump (ie. using ahoy drush)
4. If desired, export the config (ie. using ahoy drush).

### How to build a new site that is vanilla govcms with civictheme.

First update the composer.json to get the right versions you want, then
follow these steps that again are just a basic version of
[these docs](https://docs.civictheme.io/development/drupal-theme/using-in-govcms-saas).

```
# Get a vanilla govcms saas.
ddev composer update
ddev drush si govcms

# Install dependencies.
ddev drush ev "require 'themes/custom/civictheme/theme-settings.provision.inc'; civictheme_enable_modules();"
ddev drush cr

# Enable and set civictheme.
ddev drush theme:enable -y civictheme
ddev drush config-set -y media.settings standalone_url true

# Enable and set adminimal.
ddev drush theme:enable -y adminimal_theme
ddev drush config-set -y system.theme admin adminimal_theme

# Remove govcms content model. Don't forget to uninstall this helper module.
ddev drush en -y civictheme_govcms
ddev drush civictheme_govcms:remove-config --preserve=user_roles
ddev drush pm-uninstall -y civictheme_govcms

# At this point you have a baseline, this is where you need to start for any
# of the content examples below. Note that the baseline is partly broken with
# "This block is broken or missing" errors. Proceed to the next section.
# @see baseline.sql.gz
```

### Resolve errors

This does an ultra-minimal block setup, like the logo in the header.
This resolves "This block is broken or missing" errors.

1. log into the site, go to /admin/appearance/settings/civictheme
2. Scroll to CIVICTHEME CONTENT PROVISION, and run that thing. 


## Demos

The client may have seen govcms in different setups and expecting these to be replicated.
These are listed at https://www.civictheme.io/civictheme-showcase#sample-template-sites

So these instructions allow you to create the following demos from the civictheme_content
module.

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
ddev pmu civictheme_content default_content -y
```
