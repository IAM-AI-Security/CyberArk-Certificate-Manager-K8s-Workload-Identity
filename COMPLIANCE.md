# Compliance Mapping

© 2026 Go Cloud Architects — curtis@igasecurityconsulting.com

## Framework Coverage

This project maps to the following regulatory and security frameworks:

- NIST Cybersecurity Framework 2.0
- NIST SP 800-207 Zero Trust Architecture
- FFIEC IT Examination Handbook
- SOX ITGC
- PCI-DSS v4.0
- CIS Controls v8

## NIST Cybersecurity Framework 2.0

| Function | Category | Control | Implementation |
|---|---|---|---|
| PROTECT | PR.AC-1 | Identities and credentials managed | CyberArk CM SaaS manages all workload certificate identities with policy-enforced issuance |
| PROTECT | PR.AC-3 | Remote access managed | cert-manager enforces TLS for all workload-to-workload communication |
| PROTECT | PR.DS-1 | Data-at-rest protected | Private keys stored in Kubernetes secrets, never exported or logged |
| DETECT | DE.CM-7 | Monitoring for unauthorized activity | Certificate inventory in SaaS tenant provides full visibility into issued, expired, and revoked certificates |
| RESPOND | RS.MI-3 | Newly identified vulnerabilities mitigated | 1-hour certificate validity limits blast radius of any compromised workload certificate |

## NIST SP 800-207 Zero Trust Architecture

| Principle | Implementation |
|---|---|
| Never trust, always verify | Every workload must present a valid certificate issued by CyberArk CM SaaS to authenticate |
| Least privilege access | Workload certificates scoped to Client Authentication EKU only |
| Assume breach | 1-hour certificate validity ensures compromised certificates expire automatically |
| Verify explicitly | Certificate issuance requires policy compliance check against gca-workload-identity-policy |

## FFIEC IT Examination Handbook

| Domain | Requirement | Implementation |
|---|---|---|
| Authentication | Strong authentication for system components | Certificate-based mutual authentication replaces static credentials for workloads |
| Configuration Management | Secure configuration of infrastructure components | All Kubernetes manifests version-controlled; no credentials hardcoded |
| Audit and Accountability | Audit trail for privileged operations | CyberArk CM SaaS maintains full certificate issuance, renewal, and revocation history |

## SOX ITGC

| Control Objective | Implementation |
|---|---|
| Access Control | Certificate-based workload authentication; no shared static credentials |
| Change Management | Workload Issuance Policy changes require administrator access and are audited in SaaS event log |
| Audit Trail | Certificate inventory provides timestamped issuance and expiry records for examination |

## PCI-DSS v4.0

| Requirement | Description | Implementation |
|---|---|---|
| 4.2.1 | Strong cryptography for data in transit | RSA 2048, SHA-256withRSA enforced by Workload Issuance Policy |
| 8.6.1 | System accounts managed via policies | Workload identities managed through CyberArk CM SaaS policy, not manually |
| 10.2 | Audit log events | Certificate lifecycle events logged in SaaS inventory |

## CIS Controls v8

| Control | Safeguard | Implementation |
|---|---|---|
| 3 Data Protection | 3.10 Encrypt sensitive data in transit | TLS enforced for all workload communication via cert-manager |
| 5 Account Management | 5.6 Centralize account management | All workload certificate identities managed centrally in CyberArk CM SaaS |
| 12 Network Infrastructure Management | 12.8 Establish and maintain dedicated computing resources | cert-manager and venafi-agent isolated in dedicated Kubernetes namespaces |
| 16 Application Software Security | 16.5 Use up-to-date and trusted third-party components | cert-manager v1.16.2, venafi-connection v0.6.0, venafi-kubernetes-agent v1.10.1 all current releases |
