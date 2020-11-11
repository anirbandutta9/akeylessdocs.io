---
title: "Kubectl Access"
slug: "kubectl-access"
excerpt: "Short-lived certificate for accessing a Kubernetes cluster via kubectl"
hidden: false
createdAt: "2020-09-16T12:00:07.393Z"
updatedAt: "2020-09-16T13:11:03.217Z"
---
[block:api-header]
{
  "title": "Overview"
}
[/block]
Below we will describe how to configure the KubeCTL to work with short-lived certificates to access the Kubernetes cluster using the Akeyless PKI Cert Issuer. The advantage of this approach is that there is no need to manage clients' certificates. Clients identify to the Akeyless account with one of the available  Auth Methods and receive a short-lived certificate that can be used to access the Kubernetes cluster via kubectl.
[block:api-header]
{
  "title": "Setup"
}
[/block]
1. Upload the CA key together with the CA certificate  of the Kubernetes cluster into your Akeyless account (in case you are using minikube they are located in ~/.minikube/ca.key and ~/.minikube/ca.crt)
[block:code]
{
  "codes": [
    {
      "code": "akeyless upload-rsa --name myK8SCA --alg RSA2048 --rsa-key-file-path ~/.minikube/ca.key --cert ~/.minikube/ca.crt",
      "language": "shell"
    }
  ]
}
[/block]
2. Create new PKI Cert Issuer:
[block:code]
{
  "codes": [
    {
      "code": "akeyless create-pki-cert-issuer --name myK8SCertIssuer --signer-key-name myK8SCA --ttl 300 --allowed-domains minikube-user --organizations system:masters",
      "language": "shell"
    }
  ]
}
[/block]
In this case, we created an Issuer that will issue a certificate with an expiration time of up to 5 minutes with system:masters access permissions (As you can see in the RBAC Documentation the system:masters ClusterRoleBinding has full access as super-user).
[block:embed]
{
  "html": false,
  "url": "https://kubernetes.io/docs/reference/access-authn-authz/rbac/",
  "title": "Using RBAC Authorization",
  "favicon": "https://kubernetes.io/images/favicon.png",
  "image": "https://kubernetes.io/images/kubernetes-horizontal-color.png",
  "iframe": false
}
[/block]
3. On the client-side, generate a client private key (note that this key is useless without a signed certificate)

[block:code]
{
  "codes": [
    {
      "code": "openssl genrsa -out /home/user/kubectl-client.key 2048",
      "language": "shell"
    }
  ]
}
[/block]
4. On the client-side, set the Kubeconfig file to work with the Akeyless PKI Cert Issuer in order to fetch the client access certificate as follow:
[block:code]
{
  "codes": [
    {
      "code": "users:\n- name: minikube\n  user:\n    exec:\n      apiVersion: client.authentication.k8s.io/v1alpha1\n      args:\n      - get-kube-exec-creds\n      - --cert-issuer-name\n      - myK8SCertIssuer\n      - --key-file-path\n      - /home/user/kubectl-client.key\n      - --common-name\n      - minikube-user\n      command: akeyless",
      "language": "text"
    }
  ]
}
[/block]