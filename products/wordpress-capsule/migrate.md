---
description: >-
  Migrate content between WordPress Capsules with one-click synchronization of
  database, files, and configurations.
---

# Migrate

Navigate to the **Migrate** tab in your destination WordPress Capsule. Select the source Capsule from the dropdown menu. Click **Start Migration** to begin the synchronization process.

![Migration Source Selection](../.gitbook/assets/wordpress-capsule/migrate/wordpress-migration-source-selection.png)

## What Gets Migrated

Code Capsules copies the complete WordPress environment from the source Capsule:

* **Database Content:** All posts, pages, comments, and user accounts
* **Uploaded Media Files:** Images, videos, and documents from the storage Capsule
* **Installed Plugins:** Active plugins and their configurations
* **Theme Configurations:** Active theme settings and customizations

## After Migration

Once the migration is complete, the destination Capsule contains identical content to the source Capsule. The migration automatically updates internal URLs to match the destination Capsule's domain.

Both Capsules remain independent after migration. Changes made to the source Capsule after migration won't affect the destination until you run another migration.

## Common Use Cases for Migration

* **Staging to Production:** Deploy approved content from staging to your live production site.
* **Cloning Sites:** Duplicate a WordPress installation to create a new independent site.
