# Identity, access, and security foundations

## Identity model

```text
Authentication
= prove identity

Authorization
= determine allowed actions after authentication
```

Microsoft Entra ID is Azure's cloud identity and access platform for workforce and workload identities. It issues tokens and provides authentication capabilities; each resource or application still needs an authorization model.

## Identity types

| Identity | Lifecycle owner | Credential model | Primary use |
|---|---|---|---|
| User identity | Organization/identity team | Human authentication methods | Employee, guest, administrator, or customer sign-in |
| Group | Organization/identity governance | No independent sign-in | Assign access to a governed collection of identities |
| Service principal | Application/identity owner | Secret, certificate, or federated trust | Application/workload identity in a tenant |
| Managed identity | Azure manages credentials | Token obtained from Azure identity endpoint | Azure-hosted workload access without application-held credential |

An application registration describes an application identity definition. A service principal is its tenant-local identity instance. A managed identity is a special service principal whose credential lifecycle Azure manages.

```text
Azure-hosted workload supports managed identity
→ prefer managed identity

External workload can federate its identity
→ prefer workload identity federation

Neither option supported
→ service principal with certificate before a long-lived client secret, where feasible
```

An identity receives no useful access merely by existing. Grant only the required roles/scopes.

## Authorization systems

| System | Governs | Example |
|---|---|---|
| Microsoft Entra roles | Directory administration | Manage users, applications, or Conditional Access according to role |
| Azure role-based access control (Azure RBAC) roles | Azure resource management and supported data actions | Read a subscription, manage a VM, read blobs through a data role |
| Application roles/claims | Behavior inside an application or application programming interface (API) | Approver, report reader, application administrator |
| Service-native/database permissions | Resource data operations | SQL database role, New Technology File System (NTFS) access control list (ACL), Key Vault data role |

```text
Microsoft Entra role
→ directory administration

Azure RBAC role
→ Azure resource authorization at management-group, subscription,
  resource-group, or resource scope
```

RBAC inheritance simplifies consistent access but increases blast radius at higher scopes. Prefer groups, built-in roles, least privilege, and the narrowest practical scope.

## Authentication controls

- **Single sign-on (SSO):** lets a user authenticate with an identity provider and access multiple trusted applications without separately signing in to each; it does not grant authorization inside those applications.
- **Multifactor authentication (MFA):** requires additional evidence beyond a password. Prefer phishing-resistant methods for high-risk access where supported.
- **Conditional Access:** evaluates signals such as user, risk, device, application, location, and authentication strength to enforce sign-in policy.
- **Passwordless authentication:** reduces password exposure through supported strong credentials.
- **Hybrid identity:** synchronizes or federates identity between Active Directory Domain Services (AD DS) and Entra ID; the sign-in method changes dependency and outage behavior.

MFA is an authentication control requiring another factor; Conditional Access is a policy engine that evaluates context and can require MFA or another control. Conditional Access controls whether a sign-in is allowed under current conditions; it does not replace Azure RBAC or application authorization.

### Directory and external identity boundaries

- **Workforce tenant:** employees, administrators, applications, and invited business-to-business (B2B) guests collaborate under organizational policies.
- **External ID B2B collaboration:** partner identities access workforce resources without creating unmanaged local accounts; invitation, access review, and removal still need governance.
- **External tenant/customer identity and access management (CIAM):** customer identities and customer-facing application journeys need a separate lifecycle and authorization design.
- **Microsoft Entra Domain Services:** provides managed domain protocols for compatible legacy workloads; it is not a replacement for every AD DS administrative capability.

## Zero Trust foundation

Zero Trust principles:

1. Verify explicitly using all relevant signals.
2. Use least-privilege access, including time-bound privilege.
3. Assume breach and limit blast radius.

Architecture consequences include MFA/Conditional Access, Microsoft Entra Privileged Identity Management (PIM), segmentation, managed identities, private access where required, encryption, logging, and tested recovery. Zero Trust is not equivalent to "make every endpoint private"; identity, device, data, application, and operational controls remain necessary.

## Defense in depth and security posture

Defense in depth places complementary controls across physical infrastructure, identity, perimeter, network, compute, application, and data layers. A firewall does not correct excessive identity privilege; encryption does not correct an exploitable application; backup does not prevent unauthorized access.

Microsoft Defender for Cloud evaluates security posture and exposes recommendations, secure-score signals, regulatory-compliance views, and workload-protection capabilities according to enabled plans. It informs and monitors risk; it does not replace Policy guardrails, identity controls, network design, patching, or application security.

### Azure RBAC, Azure Policy, and encryption

| Mechanism | Main question | Architectural role |
|---|---|---|
| Azure role-based access control (Azure RBAC) | Who can perform which action at which Azure scope? | Authorizes principals; assignments can inherit through management scopes |
| Azure Policy | Is the resource configuration allowed or compliant? | Audits, denies, or remediates resource state; it does not grant a principal permission |

Encryption protects data confidentiality at different points: encryption at rest protects stored data, Transport Layer Security (TLS) protects data in transit, and supported confidential-computing/encryption-in-use features address selected processing threats. Azure services encrypt many stored data types by default; customer-managed keys can add key-control requirements but also add key availability, rotation, and recovery responsibilities. Encryption complements identity and authorization; it does not replace them.

## Key Vault role

Azure Key Vault protects secrets, keys, and certificates. It separates sensitive material from application code and configuration.

- Use managed identity for workload access where supported.
- Separate vaults when region, ownership, environment, compliance, or blast radius requires.
- Design soft delete, purge protection, rotation, private access/Domain Name System (DNS), logging, and regional recovery.
- Storing a secret centrally does not rotate every consumer automatically.
- Non-secret settings and feature flags normally belong in App Configuration, not Key Vault.

## Security responsibility

Microsoft secures the physical platform and managed portions of each service. The customer still owns data classification, identity, permissions, application code, configuration, monitoring, and recovery. Responsibility decreases from infrastructure as a service (IaaS) toward platform as a service (PaaS) and software as a service (SaaS), but accountability for workload outcomes does not disappear.

Detailed design: [Authentication and authorization](../Design_identity_governance_and_monitoring/authentication_and_authorization.md) and [Governance and identity governance](../Design_identity_governance_and_monitoring/governance_and_identity_governance.md).

Official references: [Azure identity, access, and security](https://learn.microsoft.com/en-us/training/modules/describe-azure-identity-access-security/), [Microsoft Entra architecture](https://learn.microsoft.com/en-us/entra/architecture/architecture), [External identities](https://learn.microsoft.com/en-us/entra/external-id/external-identities-overview), [Azure RBAC overview](https://learn.microsoft.com/en-us/azure/role-based-access-control/overview), [Managed identities](https://learn.microsoft.com/en-us/entra/identity/managed-identities-azure-resources/overview), [Defender for Cloud](https://learn.microsoft.com/en-us/azure/defender-for-cloud/defender-for-cloud-introduction).

---

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Storage foundations](storage_foundations.md) | [Prerequisites home](README.md) | [Cloud Adoption Framework →](cloud_adoption_framework.md) |
