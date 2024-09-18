{% include '/version.md' %}

# Hello Bolt

## Quest objectives

- Install Bolt.
- Verify the Bolt installation.
- Run simple commands using Bolt.

## Get started

In this quest, you will install Bolt and run some introductory commands.
As described in the previous quest, Bolt is a task runner that enables you
to invoke commands on one or more target nodes. It is well-suited to
performing actions in an ad hoc manner instead of managing a node on a
long-term basis.


## Install Bolt

<div class = "lvm-task-number"><p>Task 1:</p></div>

Pre-built Bolt packages are available for various Red Hat Enterprise Linux,
SUSE, Debian, Ubuntu, and Fedora distributions, as well as Windows and macOS.
The Learning VM is based on a CentOS (Red Hat Enterprise Linux equivalent)
7 image, so run the following command to configure the Puppet YUM repository:

    rpm -Uvh https://yum.puppet.com/puppet-tools-release-el-9.noarch.rpm

**NOTE:** If your Learning VM is configured with a host-only network, the
previous command will fail, but the `puppet-bolt` package has been cached for
convenience.

Now run this command to install Bolt:

    yum install puppet-bolt

## Verify the Bolt installation

<div class = "lvm-task-number"><p>Task 2:</p></div>

Now that you have installed Bolt successfully, let's run some commands
to verify that it works correctly.

    bolt --help

    bolt --version

<div class = "lvm-task-number"><p>Task 3:</p></div>

Bolt provides a command-line interface for running commands, scripts, tasks
and plans on the local machine or remote nodes. Now let's practice running some
simple commands:

    bolt command run 'free -th' --targets localhost

This command shows a human-readable report of the total memory allocated to
the local machine, how much is used and how much is free. The output should
look something like this:

```
Started on localhost...
Finished on localhost:
  STDOUT:
                  total        used        free      shared  buff/cache   available
    Mem:           3.7G        2.3G        143M        183M        1.2G        878M
    Swap:          1.0G         10M        1.0G
    Total:         4.7G        2.3G        1.1G
Successful on 1 node: localhost
Ran on 1 node in 0.01 seconds
```

This demonstrates trivial usage of Bolt, but since the command is running on
the local machine, you could just as easily run `free -th` to get the same
output.

Bolt is better suited to running commands on one or more remote nodes so the
output can be collected and potentially acted upon by another tool. And instead
of using a tool like ssh in a `for` loop, you provide a list of nodes to Bolt from the inventory file,
and it will connect to each one and run the desired command. Now let's
try some examples using a target node:

    cd /etc/puppetlabs/bolt    (location of the inventory file)

    bolt command run hostname --targets node-x  (x is the number of your node)

    bolt command run 'cat /etc/hosts' --targets node-x

The output from these commands will look similar to the following:

```
Started on node-1.internal.cloudapp.net...
Finished on node-1.internal.cloudapp.net:
  node-1.internal.cloudapp.net                                                                                                                                                                          Successful on 1 target: node-1.internal.cloudapp.net
Ran on 1 target in 0.68 sec
```

```
Started on node-1.internal.cloudapp.net...
Finished on node-1.internal.cloudapp.net:
  127.0.0.1   node-1 node-1.internal.cloudapp.net localhost localhost.localdomain localhost4
  ::2 localhost localhost.localdomain localhost6 localhost6.localdomain6
  10.0.1.11 puppet puppetmaster-1                                                                                                                                                                       Successful on 1 target: node-1.internal.cloudapp.net
Ran on 1 target in 0.62 sec
```

Perhaps you want to generate machine-parseable output. That is also possible
by using the `--format` option to the `bolt` command like so:

    bolt --format json command run 'cat /etc/hosts' --targets node-x

And the output will look similar to the following:

```
{ "items": [
{"target":"node-1.internal.cloudapp.net","action":"command","object":"cat /etc/hosts","status":"success","value":{"stdout":"127.0.0.1   node-1 node-1.internal.cloudapp.net localhost localhost.localdomain localhost4\n::2 localhost localhost.localdomain localhost6 localhost6.localdomain6\n10.0.1.11 puppet puppetmaster-1 \n","stderr":"","merged_output":"127.0.0.1   node-1 node-1.internal.cloudapp.net localhost localhost.localdomain localhost4\n::2 localhost localhost.localdomain localhost6 localhost6.localdomain6\n10.0.1.11 puppet puppetmaster-1 \n","exit_code":0}}
],
"target_count": 1, "elapsed_time": 0 }
```

The output can then be piped into a JSON query tool, such as `jq`, for
further processing.
bolt --format json command run 'cat /etc/hosts' --targets node-x| jq .

There are a number of other useful bolt command-line
options, and you can see them by running `bolt --help`.


## Review

In this quest, you installed Bolt, verified the installation
and practiced running some simple commands to get a feel for usage. Bolt is
a flexible task runner that can target multiple nodes at once, connect to
different types of nodes with a variety of protocols, including SSH, WinRM
and Docker, as shown in this quest.

## Additional Resources

* [Download Bolt](https://puppet.com/docs/bolt/latest/bolt_installing.html) on your own machine.
* Read the Bolt [documentation](https://puppet.com/docs/bolt/latest/bolt.html) for more details about all of its use cases.
* Dive deeper with a Bolt and Puppet tasks [self-paced course](https://learn.puppet.com/course/puppet-orchestration-bolt-and-tasks) and learn how to manage nodes with Bolt, using it from the command-line and the Puppet Enterprise Console.
