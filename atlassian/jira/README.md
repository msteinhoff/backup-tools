# JIRA Backup

Official backup guide:

https://confluence.atlassian.com/display/JIRA/Backing+Up+Data

## Backup design

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

During backup, a temporary work directory is created. Then, mysqldump is
executed with `--single-transaction` to create a consistent database backup.
The data directory is copied with `cp -r`. Everything is then packed into a
tar.gz archive.

The script writes a timestamped log to stdout/stderr, including the size of the
database dump and data directory in bytes and the checksum of the created
archive. This can be used for rudimentary consistency checks.

The backup script makes no assumption about how the archive file is processed
after it was created. Possible options:

- Write all backup data directly to a network folder (e.g. NFS)
- Copy the backup archive to another host via SSH or rsync (active copy)
- Copy the backup archive from another host via SSH or rsync (passive copy)

## Configuration

- `JIRA_HOME_DIR`

    The dedicated JIRA home directory (should contain a directory named 'data').

    Default: `/var/atlassian/application-data/jira`

- `JIRA_SCHEMA_NAME`

    The JIRA database name.

    Default: `jira`

- `BACKUP_HOME_DIR`
    
    The separate backup directory.

    Default: `/tmp/jira-backup`

- `MYSQLDUMP_PARAMS`

    Additional parameters passed to mysqldump.
    
    Usually there is no need to change this.

    Default: `--single-transaction --skip-quote-names --hex-blob`

## Example

    $ jira-backup
    (22:01:07) Creating backup 'jira-20150522-220107' in '/mnt/datastore1/jira/backup'
    (22:01:07) Creating database dump
    (22:01:09) Dumped 'jira' database schema, size: 3836788 bytes
    (22:01:09) Copying jira data directory
    (22:01:10) Copied '/mnt/datastore1/jira/home/data' directory, size: 40122228 bytes
    (22:01:10) Creating tar archive of database dump and data directory
    (22:01:13) Created tar archive '/mnt/datastore1/jira/backup/jira-20150522-220107.tar.gz', size: 33824766 bytes, SHA1: cd2ba2b68d7f409b5e92b3d8f955708f5bc4746f
    (22:01:13) Removing work directory
