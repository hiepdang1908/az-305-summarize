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
| Azure RBAC roles | Azure resource management and supported data actions | Read a subscription, manage a VM, read blobs through a data role |
| Application roles/claims | Behavior inside an application/API | Approver, report reader, application administrator |
| Service-native/database permissions | Resource data operations | SQL database role, NTFS ACL, Key Vault data role |

```text
Microsoft Entra role
→ directory administration

Azure RBAC role
→ Azure resource authorization at management-group, subscription,
  resource-group, or resource scope
```

RBAC inheritance simplifies consistent access but increases blast radius at higher scopes. Prefer groups, built-in roles, least privilege, and the narrowest practical scope.

## Authentication controls

- **Multifactor authentication (MFA):** requires additional evidence beyond a password. Prefer phishing-resistant methods for high-risk access where supported.
- **Conditional Access:** evaluates signals such as user, risk, device, application, location, and authentication strength to enforce sign-in policy.
- **Passwordless authentication:** reduces password exposure through supported strong credentials.
- **Hybrid identity:** synchronizes or federates identity between AD DS and Entra ID; the sign-in method changes dependency and outage behavior.

Conditional Access controls whether a sign-in is allowed under current conditions. It does not replace Azure RBAC or application authorization.

### Directory and external identity boundaries

- **Workforce tenant:** employees, administrators, applications, and invited B2B guests collaborate under organizational policies.
- **External ID B2B collaboration:** partner identities access workforce resources without creating unmanaged local accounts; invitation, access review, and removal still need governance.
- **External tenant/CIAM:** customer identities and customer-facing application journeys need a separate lifecycle and authorization design.
- **Microsoft Entra Domain Services:** provides managed domain protocols for compatible legacy workloads; it is not a replacement for every AD DS administrative capability.

## Zero Trust foundation

Zero Trust principles:

1. Verify explicitly using all relevant signals.
2. Use least-privilege access, including time-bound privilege.
3. Assume breach and limit blast radius.

Architecture consequences include MFA/Conditional Access, PIM, segmentation, managed identities, private access where required, encryption, logging, and tested recovery. Zero Trust is not equivalent to "make every endpoint private"; identity, device, data, application, and operational controls remain necessary.

## Defense in depth and security posture

Defense in depth places complementary controls across physical infrastructure, identity, perimeter, network, compute, application, and data layers. A firewall does not correct excessive identity privilege; encryption does not correct an exploitable application; backup does not prevent unauthorized access.

Microsoft Defender for Cloud evaluates security posture and exposes recommendations, secure-score signals, regulatory-compliance views, and workload-protection capabilities according to enabled plans. It informs and monitors risk; it does not replace Policy guardrails, identity controls, network design, patching, or application security.

## Key Vault role

Azure Key Vault protects secrets, keys, and certificates. It separates sensitive material from application code and configuration.

- Use managed identity for workload access where supported.
- Separate vaults when region, ownership, environment, compliance, or blast radius requires.
- Design soft delete, purge protection, rotation, private access/DNS, logging, and regional recovery.
- Storing a secret centrally does not rotate every consumer automatically.
- Non-secret settings and feature flags normally belong in App Configuration, not Key Vault.

## Security responsibility

Microsoft secures the physical platform and managed portions of each service. The customer still owns data classification, identity, permissions, application code, configuration, monitoring, and recovery. Responsibility decreases from IaaS toward PaaS/SaaS, but accountability for workload outcomes does not disappear.

Detailed design: [Authentication and authorization](../Design_identity_governance_and_monitoring/authentication_and_authorization.md) and [Governance and identity governance](../Design_identity_governance_and_monitoring/governance_and_identity_governance.md).

Official references: [Azure identity, access, and security](https://learn.microsoft.com/en-us/training/modules/describe-azure-identity-access-security/), [Microsoft Entra architecture](https://learn.microsoft.com/en-us/entra/architecture/architecture), [External identities](https://learn.microsoft.com/en-us/entra/external-id/external-identities-overview), [Azure RBAC overview](https://learn.microsoft.com/en-us/azure/role-based-access-control/overview), [Managed identities](https://learn.microsoft.com/en-us/entra/identity/managed-identities-azure-resources/overview), [Defender for Cloud](https://learn.microsoft.com/en-us/azure/defender-for-cloud/defender-for-cloud-introduction).
