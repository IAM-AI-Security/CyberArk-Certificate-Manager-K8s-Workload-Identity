# Model Risk Management

© 2026 Go Cloud Architects — curtis@igasecurityconsulting.com

## Applicability

This project does not deploy AI or machine learning models. No LLM inference, no automated decision models, and no AI agents are involved. SR 11-7 model risk management obligations do not apply to this deployment.

This document is included as part of the Go Cloud Architects AI Security Governance Framework standard three-document set to confirm the absence of model risk in this project and to document the boundary between machine identity management and AI-assisted identity management.

## Machine Identity vs AI Identity Management

| Aspect | This Project | AI Agent Projects |
|---|---|---|
| Decision making | Deterministic policy enforcement via CyberArk CM SaaS | LLM inference with human review gates |
| Credential issuance | Policy-driven, rule-based, no probabilistic components | AI-classified findings drive remediation actions |
| Auditability | Full certificate issuance audit trail in SaaS inventory | CloudTrail + ServiceNow ticket per remediation |
| SR 11-7 applicability | Not applicable | Applicable covered in IAM-Privilege-Drift-Agent MODEL_RISK.md |

## Human Oversight

Certificate issuance and revocation are policy-driven with no AI inference involved. Human oversight is exercised at the policy configuration layer Workload Issuance Policy settings are version-controlled and require administrator access to modify.

## Related AI Security Projects

For AI model risk documentation covering LLM-assisted identity remediation, see:

- `IAM-Privilege-Drift-Agent/MODEL_RISK.md`
- `NHI-Lifecycle-Automation-Agent/MODEL_RISK.md`
