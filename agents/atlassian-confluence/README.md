# Confluence backup

Official backup guide:

https://confluence.atlassian.com/display/DOC/Production+Backup+Strategy

## Design

Atlassian wants you to backup the database and the following confluence
data:

- `confluence.cfg.xml`
- `attachments`
- `config`
- `index`

The following assumptions are made:

- Confluence is configured to use MySQL (currently the only supported DBMS)
- MySQL credentials are stored under `/root/.my.cnf`

    *Note:* When storing plaintext credentials on disk, make sure the file is
    only readable by root (chmod `0600`).

- There is a dedicated Confluence home directory
- There is a separate directory to hold all Confluence backups
- The backup directory has enough free space available for a database dump, a
copy of all confluence directories mentioned in the list above and a tar.gz
archive of everything

During backup, mysqldump is executed with `--single-transaction` to create a
consistent database dump in the backup work directory. All confluence files and
directories are copied with `cp -r`. Everything is then packed into a tar.gz
archive.

The script writes a timestamped log to stdout/stderr, including the size of the
database dump and data directory in bytes and the checksum of the created
archive. This can be used for rudimentary consistency checks.

## Configuration

- `CONFLUENCE_HOME_DIR`

    The dedicated Confluence home directory.

    Default: `/var/atlassian/application-data/confluence`

- `CONFLUENCE_SCHEMA_NAME`

    The Confluence database name.

    Default: `confluence`

- `CONFLUENCE_VERSION_NAME`

    Confluence version name that will be written to the backup archive. This is
    just an informal reminder about the appliction version that was used to
    create the backup in case a restore is necessary.

    I did not find an easy way to automatically retrieve the version from a
    Confluence installation, so unfortunately it must be set manually when the
    application is installed or updated or (preferably) by the configuration
    management system.

    *Note:* As of now (2015-05-25), the version could e.g. retrieved from
    `confluence/META-INF/maven/com.atlassian.confluence/confluence-webapp/pom.properties`
    but I do not like the idea to automate this as it looks like an
    implementation detail that could change without notice.

    Default: `0.0.0`

- `BACKUP_HOME_DIR`

    The separate backup directory.

    Default: `/tmp/confluence-backup`

- `MYSQLDUMP_PARAMS`

    Additional parameters passed to mysqldump.

    Usually there is no need to change this.

    Default: `--single-transaction --skip-quote-names --hex-blob`

## Example

    $ confluence-backup
    (00:20:50) Creating backup 'confluence-20150525-002050' in '/mnt/datastore1/confluence/backup'
    (00:20:50) Creating database dump
    (00:20:50) Dumped 'confluence' database schema, size: 1531965 bytes
    (00:20:50) Copying confluence application data
    (00:20:50) Copied 'confluence.cfg.xml'
    (00:20:50) Copied '/mnt/datastore1/confluence/home/attachments' directory, size: 503764 bytes
    (00:20:50) Copied '/mnt/datastore1/confluence/home/index' directory, size: 141936 bytes
    (00:20:50) Bound backup archive to application version '5.7.1'
    (00:20:50) Creating tar archive of database dump and application data
    (00:20:50) Created backup archive '/mnt/datastore1/confluence/backup/confluence-20150525-002050.tar.gz', size: 635380 bytes
    (00:20:50) Checksum of backup archive '/mnt/datastore1/confluence/backup/confluence-20150525-002050.tar.gz': 6c96b6d096113f8c869b8b60a94b3f5c109715eb
    (00:20:50) Removing work directory
