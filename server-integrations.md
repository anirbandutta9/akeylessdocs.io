---
date: 2020-11-07
title: Server Integrations
---

Server Integrations
===================

\"Lightrun comes with working integrations for some of leading
monitoring, alerting and communication platforms. The list of supported
integrations is constantly expanding. You can see the updated list of
the integrations and configure each of them through the Integrations
panel (*Manager* → *System Integrations*)

::: {.note}
Manager role is required to edit integrations\"
:::

![Integrations Page](img/integrations.png)

::: {.note}
To see an agent data sent to any of the configured integration, please
make sure it's piping status is set to either \"Plugin\" or \"Both\".
:::

Statsd {#_statsd}
------

![StatsD Configuration](img/statsd.png)

1.  Press \"Connect\" button on Statsd configuration widget

2.  Fill \"Server\" and \"UDP Port\" fields with the relevant values
    (default values are displayed)

3.  Press \"Connect\" button and make sure you see the approval toast
    message

4.  Press \"Disconnect\" button to disable the configuration

Prometheus {#_prometheus}
----------

![Prometheus Configuration](img/prometheus.png)

1.  Press \"Connect\" button on Prometheus configuration widget

2.  Copy the scrape config to your prometheus.yml file

3.  Press \"Activate\" button and make sure you see the approval toast
    message

4.  To disable the integration, remove the scrape config from the
    prometheus.yaml file or press \"Deactivate\" button

Datadog {#_datadog}
-------

![Datadog Configuration](img/datadog.png)

1.  Press \"Connect\" button on Datadog configuration widget

2.  Add your Datadog API key

3.  Press \"Connect\" button and make sure you see the approval toast
    message

4.  To disable the integration press \"Disconnect\" button

Logz.io {#_logz_io}
-------

![Logz.io Configuration](img/logzio.png)

1.  Press \"Connect\" button on Logz.io configuration widget

2.  Fill \"URL\", \"Company\" and \"Type\" fields with the relevant
    values (default values are displayed)

3.  Add your Logz.io token (no default value displayed)

4.  Press \"Connect\" button and make sure you see the approval message

5.  To disable the integration, press \"Disconnect\" button

FAQ {#_faq}
---

1.  Make sure the agent piping status set to either \"Plugin\" or
    \"Both\".

2.  Wait a few minutes after sending your logs to give the platform time
    to index and make them available for search. It normally happens
    from within seconds to one minute, but sometimes it can take longer.

3.  Check for network connectivity issues, e.g. firewall configurations

**Q: I see error toast messages when pressing \"Connect\" or the
\"Activate\" button..**

    Make sure you provide proper values for the related fields. Please refer one fo the following:

-   [StatsD README](https://github.com/statsd/statsd)

-   [Prometheus FAQ](https://prometheus.io/docs/introduction/faq/)

-   [Datadog Log Collection Troubleshooting
    Guide](https://docs.datadoghq.com/logs/guide/log-collection-troubleshooting-guide/)

-   [Logz.io shipping troubleshooting
    guide](https://docs.logz.io/user-guide/log-shipping/log-shipping-troubleshooting.html)
