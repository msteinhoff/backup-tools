# Atlassian backup scripts

**WARNING: WORK IN PROGRESS, DO NOT USE YET**

This repository contains a set of backup scripts for all Atlassian products
currently used at catapullt. Our system setup is very simple and so are the
backup scripts.

# General backup design

At catapullt, we currently run Confluence, JIRA and Stash. A backup of those
applications usually includes a database schema that must be dumped to disk and
some files not stored in the database (e.g. attachments) that must be copied
somewhere. To avoid version problems on restore, we also write the application's
current version to the backup archive.

All backup jobs create a backup archive which is stored in a temporary work
directory. The default is to use a directory on the backed up system but in a
different place than the application's home directory. The location is
configurable and its possible to write directly to e.g. an NFS folder.

It is expected that the applications run on ECC RAM to prevent memory
corruption. If the backup is written to the local disk and the disk corrupts
your files, you are screwed. ¯\_(ツ)_/¯

After a backup archive has been created, a SHA1 checksum of the backup archive
is calculated and stored beneath the file.

As the backup is worthless when it stays on the backed up host, it should be
post-processed afterwards. Possible options include:

- A script on the backed up host copies the data to another backup host, then
prunes the local backup directory

- A cron job on some backup host regularly polls all to-be-backed-up systems,
new backup archives are then pulled and removed

# BOM

For each atlassian product, you get:

- a bash script that creates a backup on the local disk
- a configuration file to override default directory paths

This repository also provides a simple wrapper utility that will send execution
notifications via email.

This way, an administrator can see if the backup was successful, failed, or did
not run at all.
