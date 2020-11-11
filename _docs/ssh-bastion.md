---
title: "SSH Bastion"
slug: "ssh-bastion"
hidden: false
createdAt: "2020-10-25T12:17:47.811Z"
updatedAt: "2020-10-25T12:22:15.460Z"
---
[block:api-header]
{
  "title": "Akeyless SSH Bastion"
}
[/block]
SSH Bastion aims to traffic connections to servers that are not directly accessible via SSH, but instead directed through a bastion host, which proxies the connection between the SSH client and the remote servers.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/763fa82-CaptureASR.JPG",
        "CaptureASR.JPG",
        1770,
        397,
        "#f2f2f2"
      ]
    }
  ]
}
[/block]
Akeyless SSH bastion is based on a Docker container with two main features:
  * Support SSH signed certificate authentication with a simple setup
  * Can record all SSH sessions traffic, and expose them to the filesystem for log forwarding

command structure - host port:docker port
**ports: (-p)**
  * 9900 - Akeyless Restful API
  * 22 - SSH port

**volumes: (-v)**
-v {local_path}::/var/akeyless/conf/logand.conf ports: (-p)  - configure your SSH log forwarding, see also [SSH Log Forwarding](doc:ssh-log-forwarding)
-v {local_path}::/var/akeyless/creds ports: (-p) - location of the credentials
-v <host_filesystem_logs>:/tmp/ssh_logs - logs are written to this directory
[block:api-header]
{
  "title": "Bastion Installation"
}
[/block]
As mentioned before, the Akeyless SSH bastion is based on docker, so it's very simple to spin it up:
[block:code]
{
  "codes": [
    {
      "code": "docker pull akeyless/ssh-proxy \ndocker run -d -p 0.0.0.0:<host_ssh_port>:22 -p 0.0.0.0:9900:9900 -v <host_filesystem_creds>:/var/akeyless/creds -v <host_filesystem_logs>:/tmp/ssh_logs -v <conf_file>:/var/akeyless/conf/logand.conf akeyless/ssh-proxy:latest",
      "language": "shell"
    }
  ]
}
[/block]
Example:
[block:code]
{
  "codes": [
    {
      "code": "docker pull akeyless/ssh-proxy \ndocker run -d -p 0.0.0.0:2222:22 -p 0.0.0.0:9900:9900 -v ~/ssh-proxy/creds:/var/akeyless/creds -v ~/ssh-proxy/logs:/tmp/ssh_logs -v ~/ssh/log_forwarding.conf:/var/akeyless/conf/logand.conf akeyless/ssh-proxy:latest",
      "language": "shell"
    }
  ]
}
[/block]

[block:callout]
{
  "type": "warning",
  "body": "* The actual akeyless-ssh is available at the following link -  https://akeylesscli.s3.us-east-2.amazonaws.com/ssh/akeyless-ssh . It should be placed on a folder that is in the PATH environment variable, like /usr/local/bin/.\n* The following file contains fixed parameters such as cert issuer and profile to save passing them on each akeyless-ssh command - https://akeylesscli.s3.us-east-2.amazonaws.com/ssh/akeyless-ssh.rc . It should be saved as ~/.akeyless-ssh.rc on the client side.",
  "title": "Please Note"
}
[/block]
If you wish to configure log forwarding for [Syslog](doc:ssh-log-forwarding#syslog-configuratoin), [Spunk](doc:ssh-log-forwarding#splunk-configuration), [ELK / Logstash](doc:ssh-log-forwarding#elk--logstash-configuration), and [ELK Elasticsearch](doc:ssh-log-forwarding#elk-elasticsearch-configuration), please follow the relevant instructions. 
[block:callout]
{
  "type": "info",
  "body": "host_filesystem_creds - Make sure to keep your CA public key here, the file must be named ca.pub\nhost_filesystem_logs - All the SSH sessions will be recorded here, file per session",
  "title": "Please Note"
}
[/block]

[block:api-header]
{
  "title": "Configure Target Server"
}
[/block]
Now configure the target remote server you wish to authenticate to, by completing the following steps:
  * Place your CA public key (ca.pub) in the remote server under /etc/ssh/ca.pub
  * Now configure your remote server to trust it by adding this line in /etc/ssh/sshd_config:  TrustedUserCAKeys /etc/ssh/ca.pub
  * Run the following command in the remote server:  systemctl restart sshd
  * Run the following command, and make sure you replace remote_user and remote_server_host with the right values:

[block:code]
{
  "codes": [
    {
      "code": "./akeyless-ssh <remote_user>@<remote_server_host> via localhost:2223 --cert-issuer-name akeyless-ssh-cert --profile ldap-compose",
      "language": "shell"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "SSH Client Authentication Via Bastion"
}
[/block]
Use akeyless-ssh script to perform SSH authentication to the target server via Akeyless Proxy:
[block:code]
{
  "codes": [
    {
      "code": "./akeyless-ssh <user@remote-server[:port]> via <bastion-server[:port]> --cert-issuer-name ",
      "language": "shell"
    }
  ]
}
[/block]
After a successful login you should see Akeyless logo in the terminal, this indicates you managed to login via Akeyless bastion.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/16f57d0-09a97394-9008-4ee4-9132-946f71f42258.png",
        "09a97394-9008-4ee4-9132-946f71f42258.png",
        1092,
        276,
        "#060606"
      ]
    }
  ]
}
[/block]