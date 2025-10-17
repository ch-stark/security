BSI Grundschutz for Kubernetes: Tool Segregation Plan
The BSI Grundschutz implementation leverages three core tools, each providing a distinct type of security control to ensure continuous compliance with the APP.4.4 Kubernetes and SYS.1.6 Containerisierung modules.

1. Gatekeeper: The Enforcer (Preventive Control) 🛑
Gatekeeper's role is to act as a security guard (Validating Admission Webhook) at the cluster's entrance to prevent non-compliant resources from ever being created or updated.

Use Case (When to use)	BSI Goal Achieved	Mechanism
Pre-Deployment Enforcement	Preventive Control. Stop security violations before they start.	Rejects YAML manifests (e.g., Deployments) that violate security policy in real-time.
Runtime Security Baseline	SYS.1.6.A4 (Restricted Container Rights)	Enforce Pod Security Standards (PSS) Restricted profiles by creating Constraints to block: ➡️ Privileged containers (privileged: true), ➡️ Host path mounts, ➡️ Unapproved image registries.
Mandatory Configuration	APP.4.4.A7 (Network Separation)	Mandate that all application namespaces must have a NetworkPolicy defined to enforce micro-segmentation.
Audit Non-Blocking Violations	APP.4.4.A13 (Automated Audit)	Use its Audit function to periodically check existing resources against current policies and report violations without blocking (detective role).

Export to Sheets

2. Compliance Operator: The Auditor (Detective Control) 🔎
The Compliance Operator's role is to audit the entire cluster's configuration against security benchmarks, including underlying nodes, and generate formal, auditable reports.

Use Case (When to use)	BSI Goal Achieved	Mechanism
Formal Compliance Reporting	Formal Audit Evidence. Provide verifiable reports for internal/external BSI auditors.	Execute a scheduled ComplianceScan using a BSI-mapped security Profile (e.g., OpenSCAP checks).
Node-Level Configuration	SYS.1.6 (Containerization Security)	Audit the configuration of the Kubernetes nodes (e.g., OS hardening, host file permissions, Kubelet config), which is outside the scope of Gatekeeper.
Drift Detection (After Deployment)	APP.4.4.A13 (Automated Audit)	Periodically check configurations that are prone to drifting (e.g., checking RBAC rules, logging settings, or system resource usage).
Suggesting Fixes	Remediation. Identify exactly what needs fixing.	Automatically generate Remediation Custom Resources (CRs) that can be applied to fix violations found during the scan.

Export to Sheets

3. ACM Policies: The Governor (Centralized Management) 🌍
Advanced Cluster Management (ACM) Policies are for managing the deployment and configuration of the other tools (Gatekeeper and Compliance Operator) and their policies across multiple Kubernetes clusters.

Use Case (When to use)	BSI Goal Achieved	Mechanism
Multi-Cluster Consistency	ORP.1 (Organization) / ISMS.1 (Security Management)	Ensure the exact same set of BSI-related Gatekeeper Constraints and Compliance Operator Profiles are deployed consistently across all Dev, Staging, and Prod clusters.
Policy Rollout and Lifecycle	OPS.1.1.3 (Patch/Change Management)	Control the life cycle of security policies. Roll out a new Gatekeeper constraint to a limited number of clusters, monitor, and then propagate it to the fleet.
Centralized Status Reporting	ISMS.1 (Security Management)	Aggregate compliance and violation reports from both Compliance Operator and Gatekeeper across your entire cluster fleet into one central console/dashboard.
Enforcing Tool Deployment	OPS.1.1.7 (System Management)	Ensure that the Gatekeeper and Compliance Operator projects themselves are correctly installed, configured, and running on all managed clusters.
