# Threat Model

© 2026 Go Cloud Architects — curtis@igasecurityconsulting.com

## System Description

CyberArk Certificate Manager for Kubernetes deployed on Minikube, connected to Certificate Manager SaaS. cert-manager manages certificate CRDs. venafi-kubernetes-agent reports cluster inventory to the SaaS tenant. VenafiConnection authenticates the cluster to the tenant using an API key stored in a Kubernetes secret. Workload Issuance Policy enforces 1-hour certificate validity for workload identities.

## Trust Boundaries

| Boundary | Description |
|---|---|
| Cluster to SaaS | venafi-kubernetes-agent authenticates outbound to Certificate Manager SaaS via API key |
| Pod to cert-manager | Workloads request certificates via Kubernetes Certificate CRDs |
| cert-manager to VenafiConnection | cert-manager routes issuance requests through VenafiConnection to the SaaS tenant |
| SaaS policy enforcement | Certificate Manager SaaS enforces Workload Issuance Policy before signing |

## Risk Register

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Stolen workload certificate | Medium | Low | 1-hour validity limits exploitation window to minutes in practice |
| SaaS tenant connectivity loss | Low | High | cert-manager queues renewal attempts; existing valid certificates continue serving traffic until expiry |
| Kubernetes secret `venafi-credentials` compromised | Low | High | Rotate API key immediately; scope service account to minimum required permissions |
| Certificate sprawl across clusters | High | Medium | Centralized Workload Issuance Policy and venafi-kubernetes-agent inventory discovery prevent ungoverned issuance |
| Clock skew causing premature certificate expiry | Low | Medium | NTP synchronization required across all cluster nodes; cert-manager renewBefore buffer absorbs minor skew |
| Rogue workload requesting certificates | Medium | High | Workload Issuance Policy enforces Client Authentication EKU only; namespace-scoped RBAC restricts Certificate resource creation |
| CA compromise | Low | Critical | CyberArk CM SaaS Built-In Intermediate CA managed by CyberArk; certificate inventory enables rapid identification and revocation of affected certificates |

## OWASP LLM Top 10 2025 Mapping

Not applicable this project does not involve LLM inference or AI agent components. Threat model covers machine identity and PKI attack surface only.

## MITRE ATLAS Mapping

| Technique | ID | Mitigation |
|---|---|---|
| Credential Access via Kubernetes Secrets | AML.T0012 | API key stored in Kubernetes secret, namespace-scoped, not embedded in manifests |
| Supply Chain Compromise | AML.T0010 | Helm charts pulled from verified Venafi OCI registry, versions pinned |
| Exfiltration via Certificate Misuse | AML.T0037 | 1-hour certificate validity limits window of abuse for any stolen certificate |

## Attack Surface

### High Risk

| Threat | Impact | Mitigation |
|---|---|---|
| Kubernetes secret `venafi-credentials` compromised | API key exposed, attacker can authenticate to SaaS tenant | Rotate API key immediately; consider migrating to service account with scoped permissions |
| cert-manager RBAC misconfiguration | Unauthorized workloads can request certificates | Enforce namespace-scoped issuers; apply approver-policy for CertificateRequest approval |
| VenafiConnection pointing to wrong tenant URL | Certificates issued outside intended policy scope | VenafiConnection manifest version-controlled; URL validated at deployment |

### Medium Risk

| Threat | Impact | Mitigation |
|---|---|---|
| venafi-kubernetes-agent token compromised | Attacker gains read access to cluster certificate inventory in SaaS | Token is scoped to cluster reporting only; rotate via service account |
| Expired certificate not renewed | Workload outage | cert-manager renewBefore=15d ensures automatic renewal before expiry |

### Low Risk

| Threat | Impact | Mitigation |
|---|---|---|
| cert-manager webhook TLS misconfiguration | Webhook admission failures | cert-manager-webhook-ca manages webhook TLS automatically |

## Zero Standing Privilege Controls

- Workload certificates expire after 1 hour no long-lived credentials issued to workloads
- API key stored in Kubernetes secret, not hardcoded in manifests or container images
- cert-manager service accounts scoped to certificate CRD operations only
- venafi-kubernetes-agent scoped to cluster discovery and reporting only
