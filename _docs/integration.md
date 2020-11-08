---
date: 2020-11-07
title: Agent Integrations
---

Agent Integrations {#integrations}
==================

Every application server has its own way of customizing jvm arguments.

Tomcat {#_tomcat}
------

In order to configure Tomcat to use Lightruns agent we need to add the
path to the app war file as an additional parameter to the JAVA_OPTS:

``` {.shell}
-agentpath:<install_dir>/agent/lightrun_agent.so=--lightrun_extra_class_path=<tomcat-path>/webapps/<app-name.war>
```

Please restart the server:

``` {.shell}
./catalina.sh stop
./catalina.sh start
```

**Note:**

1.  This will make the Agent to run every time the webserver restarts.

2.  Tomcat autodeploy will not start a new agent. Therefore, after an
    autodeploy new insertions will not affect the application.

GlassFish {#_glassfish}
---------

In order to configure GlassFish to use Lightruns agent we need to add
the following to the runtime jvm options:

``` {.shell}
-agentpath:<install_dir>/agent/lightrun_agent.so=--lightrun_extra_class_path=<glassfish-domain-path>/applications/<app-name>/WEB-INF/classes/
```

There are several methods to add this jvm option:

1.  **Through the Admin panel (usually <http://localhost:4848>)**\
    Configurations → server-config → JVM Options tab.\

2.  **GlassFish CLI**\
    `asadmin create-jvm-options <new-jvm-option>`\

3.  **Manually**\
    Add the JVM option to `<glassfish-domain-path>/config/domain.xml`.

**Note:**

1.  This will make the Agent to run every time the webserver restarts.

2.  Glassfish autodeploy will not start a new agent. Therefore, after an
    autodeploy new insertions will not affect the application.

WildFly {#_wildfly}
-------

In order to configure WildFly to use Lightruns agent we need to add the
path to the app war file as an additional parameter to the JAVA_OPTS:

``` {.shell}
-agentpath:<install_dir>/agent/lightrun_agent.so=--lightrun_extra_class_path=<widlfly-deploy-path>/<app-name.war>. e.g. '--lightrun_extra_class_path=/opt/jboss/wildfly/standalone/deployments/myapp.war'
```

The following is checked on CentOS 7.6/WildFly 18 (official WildFly
Docker image)

1.  The JAVA_OPTS is in the standalone.conf file which is in
    `/opt/jboss/wildfly/bin/`

2.  The default server logs are in
    `/opt/jboss/wildfly/standalone/log/server.log`

3.  To deploy the app copy a .war file to the deployment dir
    `/opt/jboss/wildfly/standalone/deployments/`

4.  To redeploy an app, copy a new file to the deployment dir and then
    run `touch <app_name>.war` in the same dir. Then see
    `/opt/jboss/wildfly/standalone/log/server.log` to verify

5.  The above approach will stop agent from functioning properly

6.  To make it work, restart the WildFly - [How to start/stop the
    WildFly
    server](https://subscription.packtpub.com/book/networking_and_servers/9781784392413/2/ch02lvl1sec29/shutting-down-and-restarting-an-instance-via-the-cli)

Jetty {#_jetty}
-----

We need to create a new file `/var/lib/jetty/start.d/lightrun.ini` with
the following content:

``` {.bash}
--exec
-agentpath:<install_dir>/agent/lightrun_agent.so
```

Weblogic {#_weblogic}
--------

In order to configure Weblogic to use Lightruns agent we need to:

1.  Add the path to the app ear file as an additional parameter to the
    JAVA_OPTS:

``` {.shell}
-agentpath:<install_dir>/agent/lightrun_agent.so=--lightrun_extra_class_path=<weblogic-deploy-path>/<app-name.ear>. e.g. '--lightrun_extra_class_path=Oracle/Middleware/user_projects/domains/mydomain/deployments/myapp.ear'
```

1.  Tell Weblogic server to use the sun Http Handlers and not install
    its own:

``` {.shell}
-DUseSunHttpHandler=true
```

There are several methods to add those jvm options:

1.  **To all servers**\
    Add the JVM option to
    `Oracle/Middleware/user_projects/domains/<your-domain>/bin/setDomainEnv.sh`
    where JAVA_OPTIONS is set

2.  **Through the Admin panel (usually
    <http://localhost:7001/console>)**\
    Press Lock & Edit, then: Environment → Servers → \<Wanted-Server\> →
    Server Start → Arguments\

**Note:**

This will make the Agent run every time the server restarts.
