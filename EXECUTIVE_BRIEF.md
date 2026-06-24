# Why Machine Identity Governance Matters

**Go Cloud Architects | Executive Brief**
© 2026 Go Cloud Architects — curtis@igasecurityconsulting.com

## The Problem

Every application, service, and workload running in a modern enterprise uses a certificate to prove its identity and secure its communications. These certificates expire. When they expire without warning, applications stop working. The outage is immediate, the cause is not always obvious, and the business impact is measured in revenue, customer trust, and regulatory exposure.

A 2026 CyberArk study found that 56% of organizations experienced unplanned downtime caused by expired certificates in the past 24 months. The same study found that the average enterprise manages over 114,000 internal certificates with only 4 dedicated staff. At that ratio, manual certificate tracking is not a governance program. It is a best-effort process waiting for the next outage.

The problem is not awareness. Most security teams know certificates expire. The problem is visibility. Certificates are created by developers, issued by multiple CAs, scattered across Kubernetes clusters, cloud workloads, and on-premises systems, and tracked in spreadsheets that are outdated the moment they are saved. Without a centralized inventory that reflects the actual environment in real time, certificate expiry events are discovered when applications fail, not before.

## Why Existing Approaches Fall Short

Manual certificate tracking produces the spreadsheet problem: an inventory that lags the actual environment by weeks or months, assembled by engineers who have other priorities, and consulted only after something breaks.

Cloud provider native certificate tools solve the problem within a single cloud provider but create a new one: fragmented visibility across providers. A certificate inventory in AWS Certificate Manager says nothing about certificates running in Azure, GCP, or on-premises Kubernetes clusters. The enterprise has multiple partial inventories and no unified picture.

Short-lived certificate mandates are accelerating the problem. The CA/Browser Forum's move toward 47-day TLS certificate validity means organizations must rotate certificates roughly eight times per year per workload instead of once. A manual process that struggled with annual renewal cannot absorb an eight-fold increase in rotation frequency without automation.

## What This Architecture Delivers

This implementation connects a Kubernetes cluster to CyberArk Certificate Manager SaaS through cert-manager, the Kubernetes-native certificate lifecycle controller. Every certificate issued to a workload in the cluster flows through a centralized Workload Issuance Policy that enforces 1-hour validity, RSA key requirements, and approved usage constraints. cert-manager handles automated renewal transparently: the workload never sees an expired certificate because renewal happens before expiry, automatically, on every cycle.

The result is three concrete business outcomes.

**Operational resilience.** Certificate expiry events are eliminated as a source of unplanned downtime. Every certificate in scope is tracked in the CyberArk Certificate Manager SaaS inventory with issuance date, expiry, issuing CA, and workload assignment. Certificates approaching expiry trigger automated renewal. No engineer needs to remember a renewal date.

**Security posture.** A 1-hour certificate validity period is not an operational constraint. It is a Zero Standing Privilege control at the machine identity layer. A workload certificate compromised by an attacker is valid for at most one hour. The exploitation window is bounded by the policy, not by the attacker's operational tempo.

**Compliance readiness.** FFIEC, PCI-DSS, and SOX ITGC all require that cryptographic controls be managed and auditable. A centralized certificate inventory that shows every workload certificate, its issuing CA, its validity period, and its renewal history is the audit evidence those frameworks require. A collection of certificates scattered across Kubernetes namespaces with no central inventory is not.

## The Business Case in One Number

**56%** of organizations experienced unplanned downtime from expired certificates in the past 24 months according to CyberArk's 2026 machine identity research. Every one of those outages was preventable. The certificate existed. The expiry date was known. The renewal was not automated. This architecture makes that failure mode structurally impossible for every workload in scope.

*Sources: CyberArk Machine Identity Security Research 2026; CA/Browser Forum TLS Certificate Validity Requirements; FFIEC IT Examination Handbook; PCI-DSS v4.0; SOX ITGC Cryptographic Control Guidance.*
