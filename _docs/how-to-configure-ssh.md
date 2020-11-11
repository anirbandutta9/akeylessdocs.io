---
title: "How to: Configure Keyless SSH"
slug: "how-to-configure-ssh"
hidden: false
createdAt: "2020-04-11T20:02:53.884Z"
updatedAt: "2020-10-25T12:23:42.166Z"
---
[block:api-header]
{
  "title": "Introduction"
}
[/block]
Via a single sign-on, Akeyless Vault connects an ssh client to the server, using your chosen Authentication Method (or through your Identity Provider) while using existing Access Groups and Policies in your environment. 
Instead of issuing an SSH key pair - public and private, Akeyless provides ephemeral SSH certificates to allow access over standard SSH protocol, while eliminating the need for public SSH keys on the server side.
[block:callout]
{
  "type": "info",
  "body": "Akeyless SSH Certificate issuer allows to eliminate key approval and distribution of keys in static files of Hosts and Clients. More on SSH Certificates [here](doc:ssh-certificates).",
  "title": "SSH Certificate"
}
[/block]

[block:api-header]
{
  "title": "SSH Certificate Issuer"
}
[/block]
You can define several SSH CA's (certificate authority), each one can sign your SSH public keys, with ancillary data like expiration date, principals, extensions, etc.
You can sign the certificate with your own private key, or generate new one in Akeyless Vault. 
[block:api-header]
{
  "title": "Prerequisite"
}
[/block]
Prior to creating SSH cert. issuer you should upload your CA (RSA private key) for signing the client SSH certificate:
[block:code]
{
  "codes": [
    {
      "code": "akeyless upload-rsa --name your-RSA-key-name --alg RSA2048 --rsa-key-file-path Path-to-RSA.pem",
      "language": "shell"
    }
  ]
}
[/block]
Alternatively, you can create a new RSA key in Akeyless Vault:
[block:code]
{
  "codes": [
    {
      "code": "akeyless create-key --name your-RSA-key-name --alg RSA2048",
      "language": "shell"
    }
  ]
}
[/block]
In case you create a new private key, you should fetch the public key to use it on your host machine:
[block:code]
{
  "codes": [
    {
      "code": "akeyless get-rsa-public --name your-RSA-key-name",
      "language": "shell"
    }
  ]
}
[/block]
Example output:
[block:code]
{
  "codes": [
    {
      "code": "- RAW: MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA84EWkhcdbZHE7vrYKjh33fo4NC/9Xu3l3n7tTD2d4iN+c6B4hTn1IbiquSFOA89zd/GgaPmzisJ3PMqYy3cPvRJc7VWRu72wR9muOdHX3vP7bscR+fGgKuOn1XPXBPjsOmof1QKbQ3udxMRY+9NaN82zUp6itroCu/gjG9lySxwPgzIS8pgC/jD4zEhnPp+OCP20fH23wn9++o6nIResVBZXIdnYR2v3msX7+qhkkz1RIBVn1MWKfinHq7zFFG1Q6gpAs1FcbU3fsVd2wzs4WeJTKQDC5908moqwj83yKhuMFCRU1gnpileWdG5b8c1LmZ5FlB0XUp6MMV8gDakHGQIDAQAB\n- SSH: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDzgRaSFx1tkcTu+tgqOHfd+jg0L/1e7eXefu1MPZ3iI35zoHiFOfUhuKq5IU4Dz3N38aBo+bOKwnc8ypjLdw+9ElztVZG7vbBH2a450dfe8/tuxxH58aAq46fVc9cE+Ow6ah/VAptDe53ExFj701o3zbNSnqK2ugK7+CMb2XJLHA+DMhLymAL+MPjMSGc+n44I/bR8fbfCf376jqchF6xUFlch2dhHa/eaxfv6qGSTPVEgFWfUxYp+KcervMUUbVDqCkCzUVxtTd+xV3bDOzhZ4lMpAMLn3TyairCPzfIqG4wUJFTWCemKV5Z0blvxzUuZnkWUHRdSnowxXyANqQcZ",
      "language": "text"
    }
  ]
}
[/block]
Please copy only the SSH format (starting from “ssh-rsa…“) and copy it to a file on the SSH server, i.e. /etc/ssh/trusted
[block:api-header]
{
  "title": "Create SSH Certificate Issuer - CLI"
}
[/block]
The following command will create a new SSH Cert Issuer in Akeyless Vault with ancillary data.
  * **signer-key-name**: The private key to be used for certificate signing 
  * **allowed-users**: Users allowed to use the certificate (supports wildcard)
  * **principals**: A specific set of SSH Certificate principals 
  * **expiration-sec**: Certificate time period until expiration
  * **extensions**: A specific set of SSH Certificate extensions. Default extensions:  permit-X11-forwarding, permit-agent-forwarding, permit-port-forwarding, permit-pty, permit-user-rc.
[block:code]
{
  "codes": [
    {
      "code": "akeyless create-ssh-cert-issuer --name /prod/ssh-cert-issuer --signer-key-name /prod/prod-bastion --allowed-users 'ubuntu,root' --ttl 300",
      "language": "shell",
      "name": "CLI"
    }
  ]
}
[/block]

[block:callout]
{
  "type": "info",
  "title": "Please note for SSH Bastion you should run",
  "body": "akeyless create-ssh-cert-issuer --name /prod/ssh-cert-issuer --signer-key-name /prod/prod-bastion --allowed-users 'ubuntu,root' --allowed-users 'session_*' --ttl 300"
}
[/block]

[block:api-header]
{
  "title": "Create SSH Certificate Issuer - UI"
}
[/block]
The following figure depicts creation of a new SSH Cert. Issuer using the Web UI. 
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/89c744e-Screen_Shot_2020-06-04_at_14.22.19.png",
        "Screen Shot 2020-06-04 at 14.22.19.png",
        3360,
        1834,
        "#979aa2"
      ]
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "Client SSH Authentication - CLI"
}
[/block]
The following steps are performed by the user that wishes to authenticate to a remote server.
In order to sign your public key please use the following command:
[block:code]
{
  "codes": [
    {
      "code": "akeyless get-ssh-certificate --cert-username ubuntu --cert-issuer-name /prod/ssh-cert-issuer --public-key-file-path ~/.ssh/id_rsa.pub",
      "language": "shell",
      "name": "CLI"
    }
  ]
}
[/block]

[block:callout]
{
  "type": "info",
  "title": "",
  "body": "The command get-ssh-certificate returns a certificate that is signed by the private CA key and uses the client’s public key that will used to connect to the target server. The client public key is not the same as the CA’s public key. It is a local public key which should be located in the command’s path together with the client’s private key. After you run the command, the signed certificate will be placed in the same path, so you will be able to connect to the target server using the client’s private/public keys which are located on the same path."
}
[/block]
The outcome of this command will be creating a new file beside the public key, with adding a suffix to its name with -cert.pub   (~/.ssh/id_rsa-cert.pub). This is a well known convention with the OpenSSH use is during authentication.

  * **cert-username:** The username to sign in the SSH certificate
  * **cert-issuer-name:** The name of the SSH certificate issuer to use for signing
  * **public-key-file-path:** SSH public key file path for signing
[block:api-header]
{
  "title": "Configure Target Server"
}
[/block]
To enable certificate authentication you should configure the target server to trust any certificates signed by your CA's public key.
1 - Fetch the CA's public key from your Akeyless account via the following command:
    akeyless get-rsa-public --name <your-ca-key-name>
    the output should be contains two sections, one in a row format and the other in SSH format.
2 - Copy the Public Key with thw SSH format from step 1 (ssh-rsa AAAAB3NzaC1yc2EAAAA...) to a new file /etc/ssh/trusted on the server that will be accepting SSH connections.
[block:code]
{
  "codes": [
    {
      "code": "ssh-rsa AAAAB3NzaC1yc2EAAAA...",
      "language": "text",
      "name": "/etc/ssh/trusted"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "SSH Principals"
}
[/block]

[block:callout]
{
  "type": "info",
  "title": "",
  "body": "Please note that the AuthorizedPrincipalsFile is optional.\nIf you choose to use it, it needs to matched with the principals listed in the SSH Cert Issuer you created."
}
[/block]
3 - Add the lines below to /etc/ssh/sshd_config. Once done the sshd service may need to be restarted
    TrustedUserCAKeys /etc/ssh/trusted
    AuthorizedPrincipalsFile /etc/ssh/principals
[block:code]
{
  "codes": [
    {
      "code": "TrustedUserCAKeys /etc/ssh/trusted\nAuthorizedPrincipalsFile /etc/ssh/principals ",
      "language": "text",
      "name": "/etc/ssh/sshd_config"
    }
  ]
}
[/block]