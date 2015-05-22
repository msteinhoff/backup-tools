# JIRA Backup

Official backup guide:

https://confluence.atlassian.com/display/JIRA/Backing+Up+Data

## Backup design

Atlassian wants you to backup the database and the JIRA data directory.

The following assumptions are made:

- JIRA is configured to use MySQL (currently the only supported DBMS)
- MySQL credentials are stored under `/root/.my.cnf` (with chmod `0600`)
- There is a dedicated JIRA home directory
- There is a separate directory to hold all JIRA backups
- The backup directory has enough free space available for a database dump, a
copy of the JIRA data directory and a tar.gz archive.

During backup, a temporary work directory is created. Then, mysqldump is
executed with `--single-transaction` to create a consistent database backup.
The data directory is copied with `cp -r`. Everything is then packed into a
tar.gz archive.

The script writes a timestamped log to stdout/stderr, including the size of the
database dump and data directory in bytes and the checksum of the created
archive. This can be used for rudimentary consistency checks.

The backup script makes no assumption what you do with the archive file after it
was created.

Possible options:

- Write the files directory to an NFS volume
- Copy the files to another host via SSH or rsync
- Connect to the server from another host and copies the file via SSH or rsync

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
