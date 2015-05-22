# run-and-notify

This is just a generic canary script that executes a given command and then
email the result to a list of recipients. Handy to use e.g. in cron jobs or when
a full-blown monitoring system is overkill.

When the command is run, the following scenarios may happen:

- the command was successful -> the recipients are notified
- the command has failed -> the recipients are notified
- no email received -> something is complete screwed up and needs investigation

## Usage

e.g. in combination with jira-backup:

$ RECIPIENTS="test@example.org" ACTION="jira backup" /usr/local/bin/run-and-notify /usr/local/bin/jira-backup
