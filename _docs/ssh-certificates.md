---
layout: doc
title: SSH certificates
date: 2019-09-08 8:14:30 +0600
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

# SSH keys

SSH keys for password-less access is superior to passwords, but has its flaws,
especially in a data center with many actors. 

SSH certificates form a preferable solution, but they are not that well familiar trough the
industry. \[block:api-header\] { "title": "Public key authentication
without certificates" } \[/block\] 

## How it works

Let's review how authentication works
without certificates. 

A computer has one of two roles: Client or Server.
A Client initiates an SSH connection to the Server. The sshd process
runs on the Server. First, the Server proves its identity by presenting
its public host key. The Client may contain a copy of the Server's key
in its known_hosts file, in which case it trusts the Server and accepts
authentication. If the Server's key is not in known_hosts, the Client
asks the user whether it should trust the Server: \[block:code\] {
"codes": \[ { "code": "The authenticity of host 'xxx' can't be
established.`\nRSA `{=tex}key fingerprint is
SHA256:kfcwi9X8T4nMRw1OM0xDXETGcqjU26/zbM+KqNB6CKw.`\nAre `{=tex}you
sure you want to continue connecting (yes/no)?", "language": "text" } \]
} \[/block\] Humans being fundamentally inattentive, the usual user
reaction is to type "yes" without checking the fingerprint.
Unconditionally accepting the Server's authentication request is one of
the weaknesses of the non-certified public key protocol. Next, the
Client authenticates the user by sending the user's public key to the
Server. The Server accepts the key if it is contained in its
authorized_keys file. This highlights two other weaknesses: How can
users add their keys to the Server's authorized_keys if they have no
password access, and how will they manage the hundreds of copies of
their key accumulating in the data center? Another weakness: Keys never
expire. Neither users nor hosts are forced to refresh their keys.
\[block:api-header\] { "title": "How certificates change the process" }
\[/block\] 

When certificates are used, user keys are not stored in the
Server's authorized_keys, and host keys are not stored in the Client's
known_hosts. So how are keys validated? Here is where a third role comes
in, the Trusted Server. Located on the Trusted Server is a private key
named Certificate Authority (CA), which is used to digitally sign host
and user keys. The result of the signing process is a so-called
certificate. To authenticate, Client and Server now exchange
certificates instead of keys. A certificate is then validated by the
CA's public key. 

Certificates solve the above-mentioned weaknesses
inherent in non-certified public key authentication: Certificates have
an expiration date; there is no need to copy user keys to Servers and
manage key copies, because user keys are not stored on Servers. There is
one inconvenience: Users can't sign their public keys themselves, since
they have no access to the private CA key. A process must be in place to
get keys signed by a privileged operator or an application. Note that
SSH certification follows the OpenPGP standard, not SSL/TLS, and that
the certificate format is OpenPGP, not X509.