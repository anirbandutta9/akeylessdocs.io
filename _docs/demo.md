---
date: 2020-11-07
title: Demo
---

Demo {#_demo}
====

The `demo` directory has some sample java examples. We will use them in
order to demonstrate the debugging capabilities.

All the demos below assume the Lightrun backend is already installed and
running.

Prime Numbers Counter {#_prime_numbers_counter}
---------------------

This demo is located under the `demo/prime-app` directory. The file
`PrimeMain.java` has a short code that counts all the prime numbers less
than 1B.

Let's see how we can add additional log prints to this application:

### 1. Run the application {#_1_run_the_application}

Open a new shell, and then:

``` {.shell}
cd demo/prime-app
javac PrimeMain.java
java -agentpath:<install_dir>/lightrun_agent.so PrimeMain
```

Now the application is running.

We can validate that the agent registered correctly by running
`lightrunc list agents`.

We should see something like:

    0 : ID 5c84e29871f7a700010ca737UPDATE Sun, 10 Mar 2019 10:10:32 GMT

### 2. Use Lightruns CLI {#_2_use_lightruns_cli}

Open a new shell.

Let's try to print the current primes counter.

    lightrunc log 0 PrimeMain.java:20 "Current Count is {cnt}"

When we look at the application window we will see something like:

    Feb 28, 2019 2:11:29 AM PrimeMain main
    INFO: LOGPOINT: current prime is 1467954
    Feb 28, 2019 2:11:29 AM PrimeMain main
    INFO: LOGPOINT: current prime is 1467955
    Feb 28, 2019 2:11:29 AM PrimeMain main
    INFO: LOGPOINT: current prime is 1467956
    Feb 28, 2019 2:11:29 AM PrimeMain main
    INFO: LOGPOINT: Logpoint is paused due to high call rate until log quota is restored

Here we can see than our agent does not allow too many log prints to be
fired - this is intended, we cap the CPU overhead of the agent.

Not all the variables are available for exploration. Some of the debug
information might be missing due to compiler's optimizations. In order
to enhance the debug information, we should add the `-g` flag to the
compilation command (more information is available in README file).

For example in this demo application when we try to add the following
log:

    lightrunc log 0 PrimeMain.java:20 "Current Prime is {i}"

We see the following log printout in the application:

    INFO: LOGPOINT: Current Prime is Identifier i not found
    Feb 28, 2019 2:05:07 AM PrimeMain isPrime

Compiling with `-g` flag `javac -g PrimeMain.java` will enable watching
i variable value.

Web App Demo {#_web_app_demo}
------------

This demo is located under `demo/web-app` directory. Here we will show
how to debug a live production web server application using dynamic
logs.

### Build and Run the Web sSrver {#_build_and_run_the_web_ssrver}

First, let's take a look on the application.

``` {.shell}
cd demo/web-app
mvn package
java -jar  target/webapp_demo-0.0.1-SNAPSHOT.jar
```

Now the webserver is running on <http://localhost:8080>.

This webserver is a simple fibonacci series calculator. When we enter
`http://localhost:8080/<number>/` we see the `<number>` fibonacci
element.

For example, <http://localhost:8080/33> will print 5702887.

### Debug the webserver {#_debug_the_webserver}

#### 1. Run the application {#_1_run_the_application_2}

``` {.shell}
cd demo/web-app
mvn package
java -agentpath:<install_dir>/lightrun_agent.so \
     -jar  target/webapp_demo-0.0.1-SNAPSHOT.jar
```

Now the webserver is running on <http://localhost:8080>.

#### 2. Use Lightruns CLI for log insertion {#_2_use_lightruns_cli_for_log_insertion}

Looks like our simple app has an integer overflow:

``` {.shell}
curl http://localhost:8080/50 # will print -1109825406o
```

We can use Lightruns CLI for inserting logs, and see the trace of such a
request.

``` {.shell}
lightrunc log 0 main/java/Example.java:20 "{n3} = {n1} + {n2}"
```

We will see an output that looks like:

    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 1 = 0 + 1
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 2 = 1 + 1
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 3 = 1 + 2
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 5 = 2 + 3
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 8 = 3 + 5
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 13 = 5 + 8
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 21 = 8 + 13
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 34 = 13 + 21
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 55 = 21 + 34
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 89 = 34 + 55
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 144 = 55 + 89
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 233 = 89 + 144
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 377 = 144 + 233
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 610 = 233 + 377
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 987 = 377 + 610
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 1597 = 610 + 987
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 2584 = 987 + 1597
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 4181 = 1597 + 2584
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 6765 = 2584 + 4181
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 10946 = 4181 + 6765
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 17711 = 6765 + 10946
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 28657 = 10946 + 17711
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 46368 = 17711 + 28657
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 75025 = 28657 + 46368
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 121393 = 46368 + 75025
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 196418 = 75025 + 121393
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 317811 = 121393 + 196418
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 514229 = 196418 + 317811
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 832040 = 317811 + 514229
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 1346269 = 514229 + 832040
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 2178309 = 832040 + 1346269
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 3524578 = 1346269 + 2178309
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 5702887 = 2178309 + 3524578
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 9227465 = 3524578 + 5702887
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 14930352 = 5702887 + 9227465
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 24157817 = 9227465 + 14930352
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 39088169 = 14930352 + 24157817
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 63245986 = 24157817 + 39088169
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 102334155 = 39088169 + 63245986
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 165580141 = 63245986 + 102334155
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 267914296 = 102334155 + 165580141
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 433494437 = 165580141 + 267914296
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 701408733 = 267914296 + 433494437
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 1134903170 = 433494437 + 701408733
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: 1836311903 = 701408733 + 1134903170
    INFO 14911 --- [nio-8080-exec-1] com.google.DynamicLog   : LOGPOINT: -1323752223 = 1134903170 + 1836311903

Now we can see clearly the integer overflow.

Dockerized Application {#_dockerized_application}
----------------------

TODO

Scala Game Of Life {#_scala_game_of_life}
------------------

Our agent can be used also for inserting logs into scala application.
The files for this demo are located under `demo/scala-game-of-life`.
Here we will find the famous **Game Of Life** simulation.

### Build and Run the Game {#_build_and_run_the_game}

``` {.shell}
export TERM=xterm-color
cd demo/scala-game-of-life
sbt package
sbt run
```

This will print to the console every few msecond a new \'Game Of Life\'
State.

### Debug the Game {#_debug_the_game}

#### 1. Run the application {#_1_run_the_application_3}

``` {.shell}
cd demo/scala-game-of-life
sbt package
java  -agentpath:<install_dir>/lightrun_agent.so \
      -cp /home/osboxes/.sbt/boot/scala-2.12.7/lib/scala-library.jar:target/scala-2.12/game-of-life-scala_2.12-0.1.0.jar \
      de.l7r7.lab.conway.GameOfLife
```

Simulation is running.

#### 2. Use Lightruns CLI for log insertion {#_2_use_lightruns_cli_for_log_insertion_2}

``` {.shell}
lightrunc log 0 de/l7r7/lab/conway/GameOfLife.scala:85 "Board Type {board.getClass().toString()}"
```

This will insert a log print after every game simulation.

::: {.note}
Expressions must be written using the Java language syntax, like in the
example above.
:::

Groovy Prime Main {#_groovy_prime_main}
-----------------

Our agent can be used also for inserting logs into groovy application.
The files for this demo are located under `demo/groovy-app`. The file
`GroovyPrimeMain.groovy` has a short code that counts all the prime
numbers less than 1B.

### 1. Run the application {#_1_run_the_application_4}

Open a new shell, and then:

``` {.shell}
cd demo/groovy-app
groovyc GroovyPrimeMain.groovy
java -cp <path_to_groovy_jar.jar>:. -agentpath:<install_dir>/lightrun_agent.so GroovyPrimeMain
```

The application is running.

### 2. Use Lightruns CLI for log insertion {#_2_use_lightruns_cli_for_log_insertion_3}

``` {.shell}
lightrunc log 0 GroovyPrimeMain.groovy:22 "{num} is prime"
```

We will see an output that looks like:

    Feb 23, 2020 3:25:39 PM GroovyPrimeMain isPrime
    INFO: LOGPOINT: 110389847 is prime
    Feb 23, 2020 3:25:39 PM GroovyPrimeMain isPrime
    INFO: LOGPOINT: 110389871 is prime
    Feb 23, 2020 3:25:39 PM GroovyPrimeMain isPrime
    INFO: LOGPOINT: 110389879 is prime
    Feb 23, 2020 3:25:39 PM GroovyPrimeMain isPrime
    INFO: LOGPOINT: 110389913 is prime
    Feb 23, 2020 3:25:39 PM GroovyPrimeMain isPrime
    INFO: LOGPOINT: 110389933 is prime
    Feb 23, 2020 3:25:39 PM GroovyPrimeMain isPrime
    INFO: LOGPOINT: 110389963 is prime
    Feb 23, 2020 3:25:39 PM GroovyPrimeMain isPrime
    INFO: LOGPOINT: Logpoint is paused due to high call rate until log quota is restored

::: {.note}
Expressions must be written using the Java language syntax, like in the
example above.
:::
