---
date: 2020-11-07
title: CLI - Command Line Interface
---

CLI - Command Line Interface {#_cli_command_line_interface}
============================

The CLI can be used in parallel to the plugin. It's a matter of personal
preference.

When we submit a command via the plugin/CLI we often provide a line
number/file name. If they don't match the bytecode version on the app
things will behave inconsistently and fail.

Usage {#_usage}
-----

-   `lightrunc list-agents` --- List the agents

-   `lightrunc list-tags` --- List the tags

-   `lightrunc list-agents-by-tag <Tag>` --- List the agents by given
    tag

-   `lightrunc list-actions <AgentId>` --- List the actions for the
    given agent id

-   `lightrunc log <AgentId> <Filename>:<LineNumber> <Format> -expireSec` --- Insert
    a log to the given agent at the given file/line with the format
    (text of the log)

-   `lightrunc clog <AgentId> <Filename>:<LineNumber> <Format> -condition -expireSec` --- Insert
    a conditional log to the given agent at the given file/line with the
    format (text of the log)

-   `lightrunc snapshot <AgentId> <Filename>:<LineNumber> -condition` --- Insert
    a breakpoint to the given agent at the given file/line

-   `lightrunc snapshot-data <AgentId> <SnapshotId>` --- Prints the
    accumulated data for a snapshot

-   `lightrunc counter <AgentId> <Filename>:<LineNumber> <CounterName> -condition -expireSec` --- Insert
    a breakpoint to the given agent at the given file/line

-   `lightrunc set-value <AgentId> <FileName>:<LineNumber> -expireSec <VarName1=VarValue> <VarName2=VarValue>…​` --- Set
    the values of the given variables in a certain line

-   `lightrunc rm <AgentId> <LogId>` --- Removes a log from the given
    agent

-   `lightrunc rm-tag <tagName>` --- Removes a tag from the server

-   `lightrunc status` --- Prints the current status of the backend

-   `lightrunc detach <on|off>` --- Disconnects all agents and disables
    lightrun instantly (when on) until it's turned off again

-   `lightrunc print-logs <AgentId>` --- Print recent logs for the given
    agent

-   `lightrunc enable-logs <AgentId>` --- Logs for this agent id will be
    available in the `print-logs` command. Logs are still printed in the
    server.

-   `lightrunc client-logs <AgentId>` --- Logs for this agent id will be
    available in the `print-logs` command and hidden in the server!

-   `lightrunc disable-logs <AgentId>` --- Stops tracking logs for the
    given agent ID in the backend/client (this doesn't stop the log)

-   `lightrunc server-url <ServerURL>` --- Sets the server URL for the
    command line

-   `lightrunc create-company <Name> <LicenseExpiry dd/MM/yyyy> <LicensedAgents> <AgentsEnabled>` --- Creates
    a new company with an agent user.

-   `lightrunc create-user <Username> <FirstName> <LastName> <Email> <CompanyName> <Role1> <Role2>…​` --- Creates
    a new user in the given company.

-   `lightrunc login <Username>` Log in with the given user name (you
    will be prompted for password). Optionally you can add a password by
    adding a 3rd argument password followed by your password

-   `lightrunc logout` --- Logs out the currently logged in user on this
    machine

-   `lightrunc version` --- Prints the version of lightrunc and the
    backend server

-   `lightrunc listen` --- Listens to agent/log registration events and
    prints them out

-   `lightrunc -v` --- Verbose mode

-   `lightrunc user` --- Prints the details on the server for the
    currently logged in user

-   `lightrunc help` --- Print help details

There's a lot to digest here but it's mostly intuitive once you start
using it.

**Before We Begin.**

Before doing anything we must login and set the server URL. This must be
done at least once per client machine.

::: {.tip}
Login credentials are shared between the CLI and Plugin
:::

``` {.bash}
lightrunc server-url https://192.168.0.10:8080/company/DefaultCompany
```

This will set the URL of the backend to the given URL. We need to
replace `192.168.0.10:8080` with the actual server IP and port.

We can then use the login command as such:

``` {.bash}
lightrunc login myuser
```

This will produce a password prompt.

::: {.note}
For this to work we need to create a new user in the server first via
the web UI
:::

Examples {#_examples}
--------

**List Agents and Actions.**

``` {.bash}
lightrunc list agents
```

This prints the list of agents and their associated actions. It results
in output similar to this:

``` {.bash}
0 : ID 5c6e9cdef4e833279ee286a1 HOST LAPTOP-GAJ05OS8 PID 18903 UPDATE Mon, 28 Oct 2019 16:19:03 GMT
 >  ACTION 8c6e9cdef4e833279ee265f FILE PrimeMain.java LINE 20 TXT Hello {cnt}
```

**List Actions.**

``` {.bash}
lightrunc list actions 0
```

If we want to see the actions of a specific agent and not all agents we
can use list actions. Notice the last argument is the agent id.

Whenever an agent ID or log ID is used we can provide the explicit ID
e.g. in the case above it would be:

``` {.bash}
lightrunc list actions 5c6e9cdef4e833279ee286a1
```

However, providing an offset from the listing would also work in this
case. So 0 as we did before, meant the first agent printed by
`list agents`. Similarly action 0 would mean the first action within
that agent.

Next lets look at log insertions...​

**Insert Log.**

``` {.bash}
lightrunc log 0 Main.java:10 "Array size {arr.length}"
```

This command inserts a new log to the first agent. The log will print
\"Array size \" followed by the result of the expression `arr.length`.
This will only work with the first agent has a class compiled from
`Main.java` and has a variable called `arr` defined in line 10.

::: {.tip}
Inserted logs have default expiration timeout of 24 hours
:::

Lets say that we have more than one agent and we want to apply this to a
tag instead of a specific agent. We can do this:

**Insert Log to Tag.**

``` {.bash}
lightrunc log tag:TagName Main.java:10 "Array size {arr.length}"
```

All CLI commands can be used with tags instead of agent IDs. Just use
the syntax `tag:TagName` and replace the word `TagName` with the
appropriate tag.

For most cases this is pretty trivial the only difference is for
commands such as list where we need to do this:

**List all the tags.**

``` {.bash}
lightrunc list tags
```

Every action can have a condition, this is often recommended to avoid
too much information/overhead and to narrow the information.

**Conditional Log.**

``` {.bash}
lightrunc clog 5c6e9cdef4e833279ee286a1 Main.java:10  "i % 10 == 0" "Array size {arr.length}"
```

The log above will be printed when the expression `i % 10 == 0` will be
evaluated to `true`.

Once we're done with an action we need to clean up. Here's where the
`rm` command comes in handy:

**Remove Command.**

``` {.bash}
lightrunc rm 0 0
```

This removes the first action from the first agent.

::: {.note}
This works for tags and full IDs as well
:::

Lightrun comes with a special tool to \"pull the plug\" and disable
Lightrun. If there are issues where we want to rule out Lightruns blame
we can use detach to disable all agents:

**Detaches all Agents.**

``` {.bash}
lightrunc detach on
```

**Re-enable agents.**

``` {.bash}
lightrunc detach off
```

**Create company by Admin.**

``` {.bash}
lightrunc create-company <Name> <LicenseExpiry dd/MM/yyyy> <LicensedAgents> <AgentsEnabled>
```

-   \<LicenseExpiry\> Date, license expiration date. After expired,
    agents can't register.

-   \<LicensedAgents\> Numeric, Max number of allowed agents.

-   \<AgentsEnabled\> Boolean, indicates whether agents are enabled in
    the company.

This will create a new company with an agent user.

**Create user by Admin or Manager.**

``` {.bash}
create-user <Username> <FirstName> <LastName> <Email> <CompanyName> <Role1> <Role2>...
```

-   \<Username\> and \<Email\> should be unique globally

-   \<RoleX\> should be one of the following: \[ROLE_ADMIN,
    ROLE_MANAGER, ROLE_USER\]. Only Admin is authorized to create a user
    with ROLE_AGENT.

This will create a new user in the given company.
