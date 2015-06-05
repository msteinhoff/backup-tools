# JIRA Backup

Official backup guide:

https://confluence.atlassian.com/display/JIRA/Backing+Up+Data

## Design

Atlassian wants you to backup the database and the JIRA data directory.

The following assumptions are made:

- JIRA is configured to use MySQL (currently the only supported DBMS)
- MySQL credentials are stored under `/root/.my.cnf`

    *Note:* When storing plaintext credentials on disk, make sure the file is
    only readable by root (chmod `0600`).

- There is a dedicated JIRA home directory
- There is a separate directory to hold all JIRA backups
- The backup directory has enough free space available for a database dump, a
copy of the JIRA data directory and a tar.gz archive

During backup, mysqldump is executed with `--single-transaction` to create a
consistent database dump in the backup work directory. The `data` directory is
copied with `cp -r`. Everything is then packed into a tar.gz archive.

The script writes a timestamped log to stdout/stderr, including the size of the
database dump and data directory in bytes and the checksum of the created
archive. This can be used for rudimentary consistency checks.

## Configuration

- `JIRA_HOME_DIR`

    The dedicated JIRA home directory (should contain a directory named 'data').

    Default: `/var/atlassian/application-data/jira`

- `JIRA_SCHEMA_NAME`

    The JIRA database name.

    Default: `jira`

- `JIRA_VERSION_NAME`

    JIRA version name that will be written to the backup archive. This is just
    an informal reminder about the appliction version that was used to create
    the backup in case a restore is necessary.

    I did not find an easy way to automatically retrieve the version from a
    JIRA installation, so unfortunately it must be set manually when the
    application is installed or updated or (preferably) by the configuration
    management system.

    Default: `0.0.0`

- `BACKUP_HOME_DIR`

    The separate JIRA backup directory.

    Default: `/tmp/jira-backup`

- `MYSQLDUMP_PARAMS`

    Additional parameters passed to mysqldump.

    Usually there is no need to change this.

    Default: `--single-transaction --skip-quote-names --hex-blob`

## Example

    $ jira-backup
    (00:21:56) Creating backup 'jira-20150525-002156' in '/mnt/datastore1/jira/backup'
    (00:21:56) Creating database dump
    (00:21:58) Dumped 'jira' database schema, size: 3836685 bytes
    (00:21:58) Copying jira data directory
    (00:21:59) Copied '/mnt/datastore1/jira/home/data' directory, size: 40122228 bytes
    (00:21:59) Bound backup archive to application version '6.3.8'
    (00:21:59) Creating tar archive of database dump and data directory
    (00:22:02) Created backup archive '/mnt/datastore1/jira/backup/jira-20150525-002156.tar.gz', size: 33823983 bytes
    (00:22:02) Checksum of backup archive '/mnt/datastore1/jira/backup/jira-20150525-002156.tar.gz': 4f61c23507da8dd5641e41c826c24fccf2356875
    (00:22:02) Removing work directory
