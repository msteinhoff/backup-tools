# catapullt backup tools

**WARNING: WORK IN PROGRESS, DO NOT USE YET**

This repository contains a set of backup scripts to backup user generated
application data of all applications currently used at catapullt. Being a small
company with < 10 employees, our system setup is very simple and so are the
backup scripts.

# Backup design

At catapullt, we currently run a bunch of virtualized applications to support
our project management and developers.

To avoid having to backup large amounts of data, we decided on a hybrid approach
of classic system administration and infrastructure as code:

IaaC:

- Host systems are provisioned semi-automatic using a custom system.
- Host systems are configured using ansible roles.
- All provisioning information and configuration is version controlled.
- Any configuration that is on the server but not in an ansible role is seen as
non-existent.
- Ansible application roles only prepare a runtime environment (e.g.
dependencies and special system configuration) for the applications.

Classic system administration:

- Applications themselves are installed and upgraded manually, installation and
upgrade procedures are documented in simple text files.

With this system we only need to backup actual application data. as all host VMs
can be re-instanciated from configuration/code in a very short timeframe. Full
VM backups are not necessary as we do not have high DR requirements.

## Application data backup

An application data backup usually includes a database schema that must be
dumped to disk and optional some files not stored in the database (e.g.
Confluence attachments) that must be copied somewhere. To avoid version problems
on restore, we also write the application's current version to the backup
archive.

All backup jobs create a backup archive which is stored in a temporary work
directory. The default is to use a directory on the backed up host but in a
different place than the application's home directory. The location is
configurable and its possible to also write directly to e.g. an NFS folder.

It is expected that the applications run on hosts with ECC RAM to prevent memory
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

# Components

- `/agents`

    For each application, a backup agent is installed on the application host
    local backup. The agent only perform a backup to disk and log to stdout or
    stderr. Configuration is done via config file and/or environment variables.

- `/notifier`

    The notifier is installed on all backed up hosts. It wraps the execution of
    a backup agent and notifies a third party about successful and failed
    backups. This way, an administrator can see if the backup was successful,
    failed, or did not run at all.

- `/archiver`

    The archiver is installed on a backup host and is used to pull data off the
    application hosts.

# Whishlist

- Support other DBMS than MySQL for Atlassian applications
- Other notification transports than e-mail (e.g. HipChat)
- Time-series logging of backup sizes, notification on variations

# Contributing

You have found a bug or want to improvement something? Awesome! :)

To avoid frustration, please do not just fork the repository and start sending
PRs. Of course nobody prevents you from doing this, but I might reject it.

Instead, create an issue to start a discussion on what you'd like to achieve and
then we can see if it makes sense to implement here. :)
