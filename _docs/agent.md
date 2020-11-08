---
layout: doc
title: Agent
date: 2020-11-08 8:14:30 +0600
post_image: assets/images/service-icon3.png
tags: [Profile]
toc: true
custom_links:
- text: Installation
  url: /
- text: Getting Started
  url: /pages/support
- text: Product Manual
  url: /explore-topics/
  submenu:
  - text: Invoice
    url: /blog-post-left-sidebar
  - text: Payment
    url: /blog-post-right-sidebar
  - text: Another Link
    url: /tag/education
- text: Support
  url: https://support.themeix.com	
- text: Contact
  url: /pages/contact
---

Agent {#_agent}
=====

The agent is at the core of Lightrun. Setting up agents is the most
important task once the backend is in place.

::: {.tip}
For elaborate agent setups please check out the [Agent
Integrations](integration.xml#integrations).
:::

Tagging {#_tagging}
-------

Tagging lets us group agents together for common functionality under a
meaningful name. E.g. Database Servers, Staging, 18GB Machines etc.

We can use any set of tags to define an agent. This would allow binding
actions to an agent even before it was launched as well as applying an
action to a cross section of servers.

We tag an agent by editing the file `agent.metadata.json` e.g.:

``` {.json}
{
    "registration": {
      "tags": [
        {
          "name": "ProductionsAgents"
        },
        {
          "name": "JDK1.8"
        },
        {
          "name": "Ubuntu16"
        }
      ]
    }
}
```

We can add/remove entries within the \"tags\" section.

::: {.note}
Changes to this file aren't detected dynamically. An agent restart would
be required
:::

Once we bind an action to a tag it will be added implicitly to the
tagged agents. This can be useful for many cases. E.g. integration tests
can execute with a specific tag, that lets us debug an integration test
failure by binding an action based on a tag.

Tags can also be defined in the command line e.g.

``` {.bash}
java -Dlightrun.registration.tags=myTag -agentpath:/path/to/agent/lightrun_agent.so RestOfTheArgumentsHere
```

Customization {#_customization}
-------------

### Dynamic Logger {#_dynamic_logger}

The inserted logs are printed using `java.util.logging` logger. You can
customize the log prints by adding
`-Djava.util.logging.config.file=/path/to/app.properties` to the command
line.

### Agent Config {#_agent_config}

There are a few parameters we can tune in order to change the agent
configuration. The configuration file is located under
`<install_dir>/agent/agent.config`.

+-----------------------------------+-----------------------------------+
| Parameter Name                    | Explanation                       |
+===================================+===================================+
| `max_dynamic_log_rate`            | Number of log prints per second   |
|                                   | (on average).                     |
+-----------------------------------+-----------------------------------+
| `max_dynamic_log_bytes_rate`      | Number of bytes per second (on    |
|                                   | average).                         |
+-----------------------------------+-----------------------------------+
| `log_stats_time_micros`           | How often to log debugger         |
|                                   | performance stats.                |
+-----------------------------------+-----------------------------------+
| `max_condition_cost`              | Maximum cost in percentage of CPU |
|                                   | consumption of condition          |
|                                   | evaluation.                       |
+-----------------------------------+-----------------------------------+
| `max_log_cpu_cost`                | Maximum cost of dynamic logging   |
|                                   | in percentage of CPU consumption  |
|                                   | (short bursts are allowed).       |
+-----------------------------------+-----------------------------------+
| `max_snapshot_buffer_size`        | The total size in bytes we allow  |
|                                   | to evaluate when capturing a      |
|                                   | snapshot                          |
+-----------------------------------+-----------------------------------+
| `max_snapshot_frame_count`        | Max frame count we allow to       |
|                                   | collect data from when capturing  |
|                                   | a snapshot                        |
+-----------------------------------+-----------------------------------+
| `breakpoint_expiration_sec`       | Breakpoint \\ Dynamic log         |
|                                   | expiration in seconds.            |
+-----------------------------------+-----------------------------------+
| `dynamic_log_quota_recovery_ms`   | Time to pause dynamic log after   |
|                                   | it runs out of quota.             |
+-----------------------------------+-----------------------------------+
| `no_check_certificate`            | Disable certificate pinning when  |
|                                   | accessing the backend             |
+-----------------------------------+-----------------------------------+
| `ignore_quota`                    | Disable all performance safe      |
|                                   | guards when evaluating a          |
|                                   | breakpoint. **Not Recommended**   |
+-----------------------------------+-----------------------------------+
| `exceptions_monitoring_enabled`   | Enable the capturing of the       |
|                                   | application's exceptions          |
+-----------------------------------+-----------------------------------+
| `exceptions_monitoring_stdout`    | Print all of the captured         |
|                                   | exceptions to the standard output |
+-----------------------------------+-----------------------------------+
| `exceptions_report_percentage`    | Process and report only this      |
|                                   | percentage of the exceptions      |
|                                   | thrown by the debuggee (A float   |
|                                   | between 0 and 1.0).               |
+-----------------------------------+-----------------------------------+
| `exceptions_should_report_caught` | Report exceptions that were       |
|                                   | caught by the application as well |
|                                   | as exceptions that remained       |
|                                   | uncaught                          |
+-----------------------------------+-----------------------------------+
| `exceptions_max_buffer_size`      | The total size in bytes we allow  |
|                                   | to evaluate when capturing an     |
|                                   | excpetion (Same as snapshot)      |
+-----------------------------------+-----------------------------------+
| `ex                               | Max frame count we allow to       |
| ceptions_stack_trace_frame_count` | collect data from when capturing  |
|                                   | an excpetion (Same as snapshot)   |
+-----------------------------------+-----------------------------------+
| `enable_pii_redaction`            | Enable PII redaction in the       |
|                                   | agent's side (may have an effect  |
|                                   | on the application's performance) |
+-----------------------------------+-----------------------------------+

### Agent Command line arguments {#_agent_command_line_arguments}

Some configurations can be changed with a command line argument. The
command line args come after the agent's path and are separated by a
comma:
`` `-agentpath:<path-to-agent>/lightrun_agent.so=--lightrun_wait_for_init,--lightrun_init_wait_time_ms=5000` ``

+----------------------+----------------------+-----------------------+
| Parameter Name       | Explanation          | Type                  |
+======================+======================+=======================+
| `hub_retry_delay_ms` | amount of time in    | int32                 |
|                      | milliseconds to      |                       |
|                      | sleep before         |                       |
|                      | retrying failed      |                       |
|                      | requests to backend  |                       |
+----------------------+----------------------+-----------------------+
| `debugge             | amount of time in    | int32                 |
| e_disabled_delay_ms` | milliseconds to      |                       |
|                      | sleep before         |                       |
|                      | checking whether the |                       |
|                      | debugger was enabled |                       |
|                      | back                 |                       |
+----------------------+----------------------+-----------------------+
| `lightr              | additional           | string                |
| un_extra_class_path` | directories and      |                       |
|                      | files containing     |                       |
|                      | resolvable binaries  |                       |
+----------------------+----------------------+-----------------------+
| `lightru             | timeout for wait if  | int32                 |
| n_init_wait_time_ms` | li                   |                       |
|                      | ghtrun_wait_for_init |                       |
|                      | is set               |                       |
+----------------------+----------------------+-----------------------+
| `lig                 | Block application    | bool                  |
| htrun_wait_for_init` | until first time of  |                       |
|                      | fetching breakpoints |                       |
|                      | from the server      |                       |
+----------------------+----------------------+-----------------------+
| `enable_safe_caller` | Allows any method    | bool                  |
|                      | without side effects |                       |
|                      | in expressions       |                       |
+----------------------+----------------------+-----------------------+
| `ex                  | Additional methods   | string                |
| tra_blocked_methods` | to block for testing |                       |
|                      | purposes             |                       |
+----------------------+----------------------+-----------------------+
| `ex                  | Additional methods   | string                |
| tra_allowed_methods` | to allowed for       |                       |
|                      | testing purposes     |                       |
+----------------------+----------------------+-----------------------+
| `extra_              | Internal names of    | string                |
| whitelisted_classes` | additional classes   |                       |
|                      | to allow for testing |                       |
|                      | purposes             |                       |
+----------------------+----------------------+-----------------------+
| `expression_max      | Maximum number of    | int32                 |
| _classes_load_quota` | classes that the     |                       |
|                      | NanoJava interpreter |                       |
|                      | is allowed to load   |                       |
|                      | while evaluating a   |                       |
|                      | single breakpoint    |                       |
|                      | expression           |                       |
+----------------------+----------------------+-----------------------+
| `expres              | Maximum number of    | int32                 |
| sion_max_interpreter | instructions that    |                       |
| _instructions_quota` | the NanoJava         |                       |
|                      | interpreter is       |                       |
|                      | allowed to execute   |                       |
|                      | while evaluating a   |                       |
|                      | single breakpoint    |                       |
|                      | expression           |                       |
+----------------------+----------------------+-----------------------+
| `pretty_printers_max | Maximum number of    | int32                 |
| _classes_load_quota` | classes that the     |                       |
|                      | NanoJava interpreter |                       |
|                      | is allowed to load   |                       |
|                      | while formatting     |                       |
|                      | some well known data |                       |
|                      | structures           |                       |
+----------------------+----------------------+-----------------------+
| `pretty_prin         | Maximum number of    | int32                 |
| ters_max_interpreter | instructions that    |                       |
| _instructions_quota` | the NanoJava         |                       |
|                      | interpreter is       |                       |
|                      | allowed to execute   |                       |
|                      | while formatting     |                       |
|                      | some well known data |                       |
|                      | structures           |                       |
+----------------------+----------------------+-----------------------+
| `dynamic_log_max     | Maximum number of    | int32                 |
| _classes_load_quota` | classes that the     |                       |
|                      | NanoJava interpreter |                       |
|                      | is allowed to load   |                       |
|                      | while evaluating all |                       |
|                      | expressions in a     |                       |
|                      | single dynamic log   |                       |
|                      | statement            |                       |
+----------------------+----------------------+-----------------------+
| `dynamic             | Maximum number of    | int32                 |
| _log_max_interpreter | instructions that    |                       |
| _instructions_quota` | the NanoJava         |                       |
|                      | interpreter is       |                       |
|                      | allowed to execute   |                       |
|                      | while evaluating all |                       |
|                      | expressions in a     |                       |
|                      | single dynamic log   |                       |
+----------------------+----------------------+-----------------------+
| `safe_caller         | Maximum allowed size | int32                 |
| _max_array_elements` | of the array to copy |                       |
|                      | or allocate in safe  |                       |
|                      | caller (copying or   |                       |
|                      | allocating larger    |                       |
|                      | arrays is considered |                       |
|                      | to be too expensive  |                       |
|                      | MISSING              |                       |
+----------------------+----------------------+-----------------------+
| `                    | Maximum stack depth  | int32                 |
| safe_caller_max_inte | that safe caller     |                       |
| rpreter_stack_depth` | will allow           |                       |
+----------------------+----------------------+-----------------------+
| `cdbg                | additional text to   | string                |
| _description_suffix` | be appended to       |                       |
|                      | debuggee description |                       |
+----------------------+----------------------+-----------------------+
| `cdbg_cla            | Cache size for class | int32                 |
| ss_files_cache_size` | files used in safe   |                       |
|                      | method caller        |                       |
+----------------------+----------------------+-----------------------+
| `cdbg_ma             | Use this value when  | int32                 |
| x_instructions_high` | ignoring quota       |                       |
+----------------------+----------------------+-----------------------+
| `c                   | Maximum number of    | int32                 |
| dbg_max_stack_depth` | stack frames to      |                       |
|                      | unwind               |                       |
+----------------------+----------------------+-----------------------+

### Metrics {#_metrics}

The agent runs alongside a production application. Hence, it's crucial
to monitor and collect important metrics about the overhead of the
agent.

The agent prints to it's own log file (usually
`/tmp/lightrun_java_agent.INFO`) statistics every
`log_stats_time_micros` microseconds.

Listed below are some reported metrics based on the agents logfile.

#### StatsD {#_statsd}

Statsd is a network deamon listens for statistics over UDP or TCP and
aggregates the data into different backends (e.g Graphite).

There's a short script that performs on-line metric scraping from agent
log file (usually `/tmp/lightrun_java_agent.INFO`).

``` {.bash}
cd <install-dir>/agent/stats/
./statsd_reporter.py --host <statsd-hostname> --port <statsd-port> --logfile <agent-log-file.txt>
```
