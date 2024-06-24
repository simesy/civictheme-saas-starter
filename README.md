# A vanilla CivicTheme demo on GovCMS SaaS

This just gives you a vanilla GovCMS with Civictheme that you would get if you followed
[these instructions](https://docs.civictheme.io/development/drupal-theme/using-in-govcms-saas).

This is valid as of CivicTheme 1.7.1 and I'll update it whenever i work
on CivicTheme.

## Warning

This is not official documentation. Consider it like a learning repo. Thanks.

## Install

If you want to see what a basic civictheme looks like, so many you 
want to check out the blocks etc. Something like:

```
git clone git@github.com:simesy/civictheme-vanilla.git
cd civictheme-vanilla
ddev start
ddev composer install
ddev import-db --file=starter-civictheme.sql.gz
ddev drush cr
ddev drush uli
```

## Initialising your saas/civictheme site

You *don't* need to build the site, just copy the custom themes into your
SaaS project, and then import the database. So:

1. Copy the web/themes/custom into the saas /themes folder
2. Build your govcms site with Ahoy
3. Import the database dump (ie. using ahoy drush)
4. If desired, export the config (ie. using ahoy drush).

## How to build a new site that is vanilla govcms with civictheme.

First update the composer.json to get the right versions you want, then:

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
ddev en -y civictheme_govcms
ddev drush civictheme_govcms:remove-config --preserve=user_roles
ddev drush pm-uninstall -y civictheme_govcms

# Export the default config.
ddev drush cex -y
```

The only thing left is install some content. Log into the site, go to 
CivicTheme settings, and find the provision content section. There's 
probably an easier way with drush.

Export the database and zip it. I usually truncate the cache tables etc.