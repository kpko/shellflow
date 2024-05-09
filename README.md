# shellflow
An on-demand workflow engine for shell scripts like backup scripts or automation scripts

## Concept
The idea is to create simple yaml configuration files for workflows and execute them based on simple trigger rules like:
- File system changes
- Cron schedules
- Manual execution

The result should be logged and there should be simple options to monitor the state of workflow executions.

Our goal was to create a workflow engine to orchestrate scheduled backups with tools like Restic: First, use docker to create a pg_dump backup of a database. Then copy the backup to a staging folder. Then adjust a configuration file and import some secrets, then use restic to backup everything. Including an easy way to monitor the workflow. 

This could be also done with shell scripts and systemd services but on the other hand I want to learn some go on the way through.

## Non-goals
- Desired state management like Ansible
- Persistent workflow execution
- A vast collection of actions to execute, actions are just shell commands at this point
