# ArgoCD TLS Certificate

This directory contains artifacts related to the TLS certificate used by the ArgoCD service at:

argocd.idc.brownrook.net

The certificate secures the ArgoCD web interface and API endpoints exposed by the BrownRook internal data center (IDC) Kubernetes environment.

---

## Purpose

This folder documents and manages the TLS material used by the ArgoCD deployment within the BrownRook infrastructure platform.

ArgoCD is deployed using GitOps and manages applications running in the Kubernetes cluster. The TLS certificate ensures encrypted communication between users, automation systems, and the ArgoCD server.

The DNS name protected by this certificate is:

argocd.idc.brownrook.net

---

## Repository Architecture

BrownRook separates certificate **issuance**, **consumption**, and **publication** across different repositories.

| Repository | Responsibility |
|---|---|
| brownrook-pki | Certificate authority hierarchy, signing policies, and issuance process |
| brownrook-infra | Infrastructure configuration and service TLS consumption |
| brownrook-web | Public trust chain publication (root and intermediate certificates only) |

This directory belongs to the **infrastructure layer**, meaning it documents how TLS is used by the ArgoCD service.

---

## Files

Typical contents of this directory may include:

argocd.idc.brownrook.net.csr   Certificate Signing Request  
argocd.idc.brownrook.net.crt   Issued certificate (optional)  
README.md                      Documentation for this service certificate  

Private keys must **never be committed** to the repository.

---

## Security Rules

Private keys are considered sensitive infrastructure secrets and must not be stored in Git.

The following rule should always apply:

PrivateKey ∉ Git

The private key associated with this certificate should be generated and stored securely outside the repository, typically in:

- Kubernetes TLS secrets
- secure secret storage systems
- future secrets management platforms such as OpenBao or Vault

The repository `.gitignore` should include patterns such as:

*.key  
*.pem  

---

## Certificate Issuance

Certificates for the `idc.brownrook.net` zone are issued by the BrownRook internal PKI hierarchy.

Signing authority:

BrownRook Root CA  
    ↓  
BrownRook Intermediate CA  
    ↓  
Service Certificate  

This hierarchy is maintained in the `brownrook-pki` repository.

---

## Future Automation

As the platform evolves, certificate management will transition toward automated issuance using Kubernetes tooling such as:

- cert-manager
- internal PKI services
- centralized secrets management platforms

Under automated management, TLS certificates will be generated dynamically and stored directly in Kubernetes secrets rather than as files in this repository.

---

## Related Services

ArgoCD is part of the BrownRook internal platform architecture.

Primary access point:

https://argocd.idc.brownrook.net

This service acts as the GitOps deployment controller responsible for synchronizing Kubernetes workloads from Git repositories into the cluster.

---

## BrownRook Platform

BrownRook is a personal platform engineering environment exploring:

- Kubernetes platform operations
- GitOps deployment workflows
- infrastructure automation
- public key infrastructure
- secure service architectures

Additional information is available at:

https://www.brownrook.com