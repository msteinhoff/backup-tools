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

    $ stash-backup
    (19:48:09) Executing stash backup client
    2015-06-05 19:48:14,976 INFO         Initializing
    2015-06-05 19:48:20,941 INFO         Using Stash 3.7.0
    2015-06-05 19:48:21,323 INFO         Contacting Stash
    2015-06-05 19:48:21,617 INFO         Stash has been locked for maintenance. It may be unlocked with token: cc8d22828e07758d792235137ffb103c1eba8968
    2015-06-05 19:48:21,995 INFO         Starting database backup on Stash. It may be cancelled with token: 55c2c8140680cf0cdba75034a1978a101d7a20cb
    2015-06-05 19:48:26,286 INFO   (13%) Verifying files in Stash home "/mnt/datastore1/stash/home"
    2015-06-05 19:48:35,592 INFO   (50%) Verifying Stash home
    2015-06-05 19:48:43,571 INFO   (50%) Backing up Stash home
    2015-06-05 19:48:50,347 INFO   (56%) Backing up Stash home
    2015-06-05 19:48:55,350 INFO   (58%) Backing up Stash home
    2015-06-05 19:49:00,254 INFO   (60%) Backing up Stash home
    2015-06-05 19:49:09,690 INFO   (61%) Backing up Stash home
    2015-06-05 19:49:14,742 INFO   (61%) Backing up Stash home
    2015-06-05 19:49:21,457 INFO   (68%) Backing up Stash home
    2015-06-05 19:49:32,674 INFO   (71%) Backing up Stash home
    2015-06-05 19:50:09,190 INFO   (73%) Backing up Stash home
    2015-06-05 19:50:14,190 INFO   (73%) Backing up Stash home
    2015-06-05 19:50:23,108 INFO   (90%) Backing up Stash home
    2015-06-05 19:50:28,157 INFO   (90%) Backing up Stash home
    2015-06-05 19:50:33,058 INFO   (98%) Waiting on Stash database backup to complete
    2015-06-05 19:50:35,248 INFO  (100%) Waiting on Stash database backup to complete
    2015-06-05 19:50:40,248 INFO  (100%) Stash has completed backing up the database
    2015-06-05 19:50:40,443 INFO  (100%) Downloading database backup zip from Stash
    2015-06-05 19:50:40,727 INFO  (100%) Backup complete: /mnt/datastore1/stash/backup/backups/stash-20150605-195040-720.tar
    2015-06-05 19:50:40,734 INFO  (100%) Unlocking Stash using token: cc8d22828e07758d792235137ffb103c1eba8968
    (19:50:41) Created backup archive '/mnt/datastore1/stash/backup/backups/stash-20150605-195040-720.tar', size: 3049226240 bytes
    (19:51:36) Checksum of backup archive '/mnt/datastore1/stash/backup/backups/stash-20150605-195040-720.tar': e742a5783dc0242768b9b8d90dff715c463a7c1b
