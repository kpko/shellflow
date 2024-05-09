# shellflow
An on-demand workflow engine for shell scripts like backup scripts or automation scripts

## Concept
The idea is to create simple yaml configuration files for workflows and execute them based on simple trigger rules like:
- File system changes
- Cron schedules
- Manual execution

The result should be logged and there should be simple options to monitor the state of workflow executions.

My goal was to create a workflow engine to orchestrate scheduled backups with tools like Restic: First, use docker to create a pg_dump backup of a database. Then copy the backup to a staging folder. Then adjust a configuration file and import some secrets, then use restic to backup everything. Including an easy way to monitor the workflow. 

This could be also done with shell scripts and systemd services but I want to learn some go on the way through and so here we go.

## Non-goals
- Desired state management like Ansible
- Persistent workflow execution
- A vast collection of actions to execute, actions are just shell commands at this point

# Yaml format
Yaml is an okay format for configuring workflows IMHO, as long as it doesn't get bloated like Kubernetes configuration files and you have to learn 800 concepts first. This is why I try to incorporate a minimalism first approach here.

The simplest possible workflow executes one action, is named by the file name of the definition and triggers manually via the shellflow command line utility.

testflow.yaml:
```yaml
steps:
- cmd: echo "hello world!"
```

The flow can be started through the `sf testflow.yaml` command.

There can be automated triggers like cron schedules and file system changes. A flow can have multiple triggers and can always be started manually.

```yaml
on:
- cron: @hourly
- watch: **/*.yaml
steps:
- cmd: echo "hello world!"
```

There is an included action called 'if' to conditionally execute paths. 'if' uses a javascript like expression parser.

```yaml
on:
- cron: @hourly
- watch: **/*.yaml
steps:
- if: $SF_TRIGGER == 'cron'
  then:
  - cmd: echo "hello from cron"
  else:
  - cmd: echo "hello from changed file $SF_TRIGGER_WATCH_FILENAME"
```

If not otherwise defined, workflows are executed in an automatically created temp folder.

```yaml
on:
- cron: @hourly
steps:
- cmd: echo "Worfklow $SF_FLOW_NAME/$SF_FLOW_ID executes in $PWD"
```

Outputs "Workflow testflow/r4gb498q executes in /tmp/shellflow/testflow/r4gb498q". The folder gets deleted after the execution in the default configuration, although this can be configured otherwise of course.
