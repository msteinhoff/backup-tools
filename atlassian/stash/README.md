# Stash backup

Official backup guide:

https://confluence.atlassian.com/display/STASH/Data+recovery+and+backups

## Backup design

Stash can be backuped up in two different ways:

- using the official backup client, recommended for most production use cases.

    See also: https://confluence.atlassian.com/display/STASH/Using+the+Stash+Backup+Client

- using a DIY-style backup with e.g. shell scripts, required in special cases.

    See also: https://confluence.atlassian.com/display/STASH/Using+Stash+DIY+Backup

Because we only run stash in a single instance and have no hard availability
requirements, stash-backup is just a simple wrapper for the stash backup client
which can be used in the same way as the JIRA and Confluence backup scripts. It
could also be extended to implement a DIY backup in the future, but we currently
have no use case for this.

The following assumptions are made (see also docs for stash backup client):

- There is a dedicated Stash home directory
- There is a separate directory to hold all Stash backups
- The backup directory has enough free space available for a backup archive
produced by the stash backup client
- The `java` executable is available on the PATH so the backup client can be
executed

During backup, the backup client is executed which then creates a .tar archive.

The script writes a timestamped log to stdout/stderr, including the size of the
backup archive in bytes and the checksum of the created archive. This can be
used for rudimentary consistency checks.

## Configuration

- `STASH_BACKUP_CLIENT_JAR`

    Absolute path to the `stash-backup-client.jar` file provided by the backup
    client distribution.

    Default: `/opt/atlassian/stash-backup/stash-backup-client.jar`

- `STASH_HOME_DIR`

    Value of the `stash.home` java property.
    
    From the Stash backup client documentation:

    > Defines the location of the home directory of the Stash instance you wish
    > to back up or restore to.
    
    Default: `/var/atlassian/application-data/stash`

- `STASH_USER`

    Value of the `stash.user` java property.

    From the Stash backup client documentation:

    > Defines the username of the Stash user with administrative privileges you
    > wish to perform the backup.

    Default: Not set

- `STASH_PASSWORD`

    Value of the `stash.password` java property.

    From the Stash backup client documentation:

    > Defines the password of the Stash user with administrative privileges you
    > wish to perform the backup.

    Default: Not set

- `STASH_BASEURL`

    Value of the `stash.baseUrl` java property.

    From the Stash backup client documentation:

    > Defines base URL of the Stash instance you wish to back up. E.g.
    > http://localhost:7990/stash or http://stashserver/

    Default: Not set

- `BACKUP_HOME_DIR`
    
    Value of the `backup.home` java property.

    From the Stash backup client documentation:

    > Defines where the Backup Client will store its own files, such as backup
    > archives.

    Default: `/tmp/jira-backup`

## Example

    TODO, no internet on ICE 512 and no stash VM available to test the script :(
