---
title: "SSH Log Forwarding"
slug: "ssh-log-forwarding"
hidden: false
createdAt: "2020-08-03T03:09:16.169Z"
updatedAt: "2020-10-25T12:14:33.919Z"
---
SSH log forwarding enables forwarding of the recordings of SSH sessions to customer log repository.
[block:api-header]
{
  "title": "Prerequisites"
}
[/block]
In order to configure log forwarding you need to follow the instructions [here](doc:how-to-configure-ssh#akeyless-ssh-bastion).
[block:api-header]
{
  "title": "Syslog Configuratoin"
}
[/block]
Edit logand.conf:
[block:code]
{
  "codes": [
    {
      "code": "target_syslog_tag=\"ssh-audit-export\"\ntarget_log_type=\"syslog\"\ntarget_syslog_network=\"udp\"\ntarget_syslog_host=\"<host>:<port>\"",
      "language": "shell"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "Splunk configuration:"
}
[/block]
Prerequisites: Splunk HTTP Event Collector: https://docs.splunk.com/Documentation/Splunk/8.0.4/Data/UsetheHTTPEventCollector
[block:code]
{
  "codes": [
    {
      "code": "target_log_type=\"splunk\"\ntarget_splunk_sourcetype=\"<your_sourcetype>\"\ntarget_splunk_source=\"<your_source>\"\ntarget_splunk_index=\"<your_index>\"\ntarget_splunk_token=\"<your_token>\"\ntarget_splunk_url=\"<your_splunk_host_address>\"",
      "language": "shell"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "ELK / Logstash Configuration"
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "target_log_type=\"logstash\"\ntarget_logstash_dns=\"localhost:8911\"\ntarget_logstash_protocol=\"tcp\"",
      "language": "shell"
    }
  ]
}
[/block]
Configure your Logstash to use the same port and protocol:
Add to logstash.conf:
input { tcp { port => 8911 codec => json } }
[block:api-header]
{
  "title": "ELK Elasticsearch Configuration"
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "target_log_type=\"elasticsearch\"\ntarget_elasticsearch_host=\"host\"\ntarget_elasticsearch_nodes=\"http://host1:9200,http://host2:9200\"",
      "language": "shell"
    }
  ]
}
[/block]