---
title: "Zero Trust RDP Access"
slug: "rdp"
hidden: false
createdAt: "2020-08-18T08:03:43.920Z"
updatedAt: "2020-10-05T06:49:15.139Z"
---
[block:api-header]
{
  "title": "Run"
}
[/block]
Simply run the container:
[block:code]
{
  "codes": [
    {
      "code": "docker run --rm -itd \\\n    -p 8888:8888 \\\n    akeyless/zero-trust-rdp-access",
      "language": "shell"
    }
  ]
}
[/block]

[block:callout]
{
  "type": "warning",
  "body": "Please wait 60 sec to allow the docker to finish the init process."
}
[/block]
Web interface will be available at port :8888 (or any other port you chose to map).
[block:api-header]
{
  "title": "Setup RDP Secret Producers with Zero Trust RDP Access"
}
[/block]

[block:callout]
{
  "type": "info",
  "body": "Please note that you need to enable OpenSSH on your machine according to the below link\nhttps://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse",
  "title": "Windows Prerequisite"
}
[/block]
1. Create and configure an RDP Dynamic Secret Producer in your Akeyless API Gateway, remember the name you used (for example, /windows).

2. Create SAML authentication method in Akeyless, and grant required permissions to it. For example, if this authentication method only requires access to /windows dynamic secret, that's the only permission you need to grant to the role associated with it.

3. Navigate to the address of Akeyless Zero Trust RDP Access container using your favorite browser (https://rdp.mycompany.com).

4. Fill in the required connection information:

  * **Akeyless Zero Trust RDP Address:** URL of Akeyless Zero Trust RDP Access (by default, it is the same address you visited in the previous step)
  * **Remote Windows server address:** IP address or hostname of the remote Windows host that you would like to connect to.
  * **Akeyless Access ID:** Access ID in Akeyless to authenticate with (the same one you created earlier). This authentication method must be able to get temporary access credentials to your Windows host using Akeyless RDP Producer.
  * **Dynamic Secret name for RDP Producer:** dynamic secret name as specified in Akeyless. Please use the full path (/windows-hosts/ds-rdp-dev-1) 
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/7c91010-2e081c6e-dc43-4f2f-9ff5-5d38adc9e810.png",
        "2e081c6e-dc43-4f2f-9ff5-5d38adc9e810.png",
        368,
        501,
        "#ccd7f2"
      ]
    }
  ]
}
[/block]
Click "Generate" button to create a permanent link to the connection you just defined. This link can be shared safely with anybody. Only the people who have access to Akeyless Access ID you used, and to the dynamic secret you specified, will be able to login to the remote Windows host using temporary credentials (that are never revealed!).

Once the link is ready, you can copy it and use to open a new connection. For example, you can bookmark the link to have instant access to your Windows host.

Clicking "Connect!" button will immediately start a new RDP session using the configuration you provided.
[block:api-header]
{
  "title": "Configure"
}
[/block]
Some configuration is supported to extend the default functionality.

** Using Akeyless API Gateway**

If your deployment includes Akeyless API Gateway, you can use it to access your remote Windows machines:
[block:code]
{
  "codes": [
    {
      "code": "docker run --rm -itd \\\n    -p 8888:8888 \\\n    -e AKEYLESS_URL=https://akeyless.mydomain.com \\\n    akeyless/zero-trust-rdp-access",
      "language": "shell"
    }
  ]
}
[/block]
In this case, https://akeyless.mydomain.com is an address of REST API exposed by Akeyless API Gateway (:8080 by default).

** Exposing RDP session recordings**

Every RDP session is recorded and saved inside the container. To access the recordings, mount a local directory to /home/akeyless/recordings path inside the container:
[block:code]
{
  "codes": [
    {
      "code": "docker run --rm -itd \\\n    -p 8888:8888 \\\n    -v `pwd`/recordings:/home/akeyless/recordings \\\n    akeyless/zero-trust-rdp-access",
      "language": "shell"
    }
  ]
}
[/block]
In this case, recordings folder will be created in your current working directory, and all sessions recordings will be available in it at the end of each session.
[block:callout]
{
  "type": "warning",
  "body": "The recordings of RDP sessions consume significant storage space. Make sure to setup backup/cleanup process to ensure smooth experience.",
  "title": "Please note"
}
[/block]
In this case, recordings folder will be created in your current working directory, and all sessions recordings will be available in it at the end of each session.

** Automatic upload of recordings to S3**

Currently, there are two options:

1. Deploy Akeyless Zero Trust RDP Access on AWS EC2, and use IAM roles for Amazon EC2 with sufficient permissions to upload new files into an S3 bucket of your choice. Then, provide AWS_REGION, AWS_S3_BUCKET and AWS_S3_PREFIX environment variables when creating a new container. All recordings will be uploaded into that bucket, with the specified prefix (path):
[block:code]
{
  "codes": [
    {
      "code": "docker run --rm -itd \\\n    -p 8888:8888 \\\n    -e AWS_REGION=us-east-1 \\\n    -e AWS_S3_BUCKET=my-bucket \\\n    -e AWS_S3_PREFIX=akeyless-zero-trust-rdp \\\n    akeyless/zero-trust-rdp-access",
      "language": "shell"
    }
  ]
}
[/block]
2. Alternatively, run the container with explicit credentials. This way is not recommended, but still possible:
[block:code]
{
  "codes": [
    {
      "code": "docker run --rm -itd \\\n    -p 8888:8888 \\\n    -e AWS_ACCESS_KEY_ID=******************** \\\n    -e AWS_SECRET_ACCESS_KEY=**************************************** \\\n    -e AWS_REGION=us-east-1 \\\n    -e AWS_S3_BUCKET=my-bucket \\\n    -e AWS_S3_PREFIX=akeyless-zero-trust-rdp \\\n    akeyless/zero-trust-rdp-access",
      "language": "shell"
    }
  ]
}
[/block]
Regardless of the method you choose, in the end of each session, recordings will be uploaded to S3 in addition to being available under /home/akeyless/recordings (via a volume mount to your local directory).

Upload status is reflected in container logs. Make sure to check them for errors if something seems wrong.