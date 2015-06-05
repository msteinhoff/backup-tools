# retrieve-host-backup

The retrieve-host-backup program enables us to operate a host system for
centralized backup storage.

## Design

The program sits on a central backup host with a shitload of disks that
hopefully never run out of space. Of course, disk monitoring and alerts are a
clever way to work around this issue. On each backed up system, agent programs
coordinate local backups which are then pulled to the central server regularly

This architecture provides two key benefits:

1. No network dependency.

    Initially backup archives are saved to local disk on a backed up host and
    backups continue to work even if there is a temporary network outage. (Until
    the backed host runs out of disk space).

2. Encapsulated backup archives.

    A backed up host does not know anything about the backup host itself. This
    way, an attacker that gains access to a backed up host is unable to use it
    as a bridge head to further penetrate the network via the backup host or
    (worse) to manipulate or corrupt existing backup archives.

## Implementation

The program uses the simple but effective combination of rsync/ssh to copy files
around. It can also be wrapped with the notifier program to report success and
error status of the archiving process.
