---
date: 2020-11-07
title: Motivation
---

Motivation {#_motivation}
==========

To identify the root cause of a production problem (logical bug,
performance issue, downtime, etc.), developers either:

1.  Review runtime logs / performance metrics

2.  Attempt to reproduce the problem in a development / test environment

Log files and APM solutions are great sources of information - they can
often identify defects in an application, but they're mostly defined
during the development stage. With the adoption of microservices and
serverless architectures, the gap between production and development
environment widens. This gap makes it hard to reproduce or anticipate
production-only issues. Lightrun bridges the gap between development and
production debugging.

**Lightrun bridges the gap between development and production
debugging.**

Overview {#_overview}
--------

Lightrun is divided into 3 parts:

1.  **Java Agent** - JVMTI agent that runs alongside the application
    which is responsible for the dynamic insertion of commands.

2.  **Backend Server** - Lightruns server. Responsible for service
    management

3.  **Client** - IDE plugin and command line utility both of which let
    us add, remove and modify the actions

![Hello Lightrun Architecture](img/diagram.png)

Limitation {#_limitation}
----------

Some debug information might be missing due to compiler's effort to
reduce the binary size. Hence, not all the variables will be visible for
exploration.

Enhancing the debug information is **highly recommended**. We can
achieve that by adding `-g` flag to the compilation command.

If the project uses Maven we should add the following lines to
`pom.xml`:

``` {.xml}
<compilerArgs>
    <arg>-g:source,lines</arg>
</compilerArgs>
```

This flag will have no impact on the compiler optimization. It will only
enhance the bytecode with additional debug info. As such the performance
of the app will remain the same.
