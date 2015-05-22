# Atlassian backup scripts

**WARNING: WORK IN PROGRESS, DO NOT USE YET**

This repository contains a set of backup scripts for all Atlassian products
currently used at catapullt. Our system setup is very simple and so are the
backup scripts.

# BOM

For each atlassian product, you get:

- a bash script that creates a backup on the local disk
- a configuration file to override default directory paths

This repository also provides a simple wrapper script that will email execution
notifications. This way, an administrator can see if the backup was success,
failed, or did not run at all.
