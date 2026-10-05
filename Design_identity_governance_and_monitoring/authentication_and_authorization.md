# Authentication and authorization

## Identity model

```text
Authentication = Who are you?
Authorization  = What may you do?

Microsoft Entra ID  → identity provider, tokens, users, groups, workloads
Conditional Access  → policy evaluation for sign-in context
Entra roles         → administer directory resources
Azure role-based access control (Azure RBAC) → authorize Azure resource control/data actions at scope
Application roles   → authorize inside an application
Key Vault           → protect secrets, keys, and certificates
```

## Authentication and identity choices

| Requirement | Recommended direction | Notes |
|---|---|---|
| Workforce cloud identity and single sign-on (SSO) | Microsoft Entra ID | Central identity, tokens, multifactor authentication (MFA)/passwordless, Conditional Access |
| Existing Active Directory Domain Services (AD DS) identities need cloud access | Hybrid identity with Microsoft Entra Connect Sync or Cloud Sync | Select based on topology, attributes, writeback, resilience, and supported features |
| Simplest resilient hybrid sign-in | Password hash synchronization (PHS) | Cloud authentication; password hashes are synchronized, not plaintext passwords |
| Validate password against on-premises AD DS | Pass-through authentication (PTA) | Requires on-premises agents; availability depends on agent design |
| Specialized federation or third-party sign-in requirement | Federation | Highest operational complexity; do not select by default |
| Managed domain services in Azure without managing domain controllers | Microsoft Entra Domain Services | Provides managed domain join, Lightweight Directory Access Protocol (LDAP), Kerberos/NT LAN Manager (NTLM); not equivalent to full self-managed AD DS |
| Partner/guest collaboration in workforce tenant | Microsoft Entra External ID business-to-business (B2B) collaboration | Guest identity remains governed in the resource tenant |
| Customer identity for a new consumer/business application | Microsoft Entra External ID in an external tenant | Current customer identity and access management (CIAM) direction for new designs |
| Context-aware access policy | Conditional Access | Signals can include user, risk, device, location, client, app, and authentication strength |
| Passwordless or phishing-resistant authentication | Windows Hello for Business, FIDO2/passkeys, or certificate-based methods as appropriate | Availability/licensing and user/device support vary |

### Current CIAM terminology

Microsoft Entra External ID covers external collaboration and customer identity scenarios. The business-to-consumer (B2C) product Azure AD B2C is no longer available for purchase by new customers as of May 1, 2025; existing customers remain supported under Microsoft's published lifecycle. Use External ID for new CIAM designs. Older Learn/practice material may still say Azure AD B2C.

### Authentication control comparison

| Choice | What it does | Choose when | Does not replace |
|---|---|---|---|
| Multifactor authentication (MFA) | Requires more than one authentication factor | A stronger sign-in proof is required; prefer phishing-resistant methods for high-risk access | Context-aware policy, authorization, or least privilege |
| Conditional Access | Evaluates identity, app, device, location, risk, and authentication context/strength to make an access decision | Requirements vary by sign-in context or risk | The authentication method itself or resource permission |
| Identity Protection | Detects and reports risky users/sign-ins and supplies risk signals | Risk-based investigation and policy response are required | Conditional Access enforcement or security operations |

Conditional Access can require MFA, but the two are not synonyms: MFA is an authentication control; Conditional Access is the policy engine that can require, block, or constrain access from evaluated signals.

## Hybrid identity trade-offs

| Option | Authentication location | Dependency during sign-in | Choose when |
|---|---|---|---|
| PHS | Microsoft Entra ID | Cloud sign-in can continue through on-premises outage | Lowest operational dependency and acceptable hash synchronization |
| PTA | On-premises AD DS through agents | Requires functioning agents and connectivity | On-premises validation is mandatory |
| Federation | External federation service | Federation infrastructure and certificates are critical | A required protocol/policy cannot be met by managed authentication |

Use seamless SSO only as a user-experience feature; it is not the authentication method itself. Design emergency access accounts so Conditional Access or federation failure does not lock out the tenant.

## Authorization planes

| Mechanism | Governs | Scope examples | Does not replace |
|---|---|---|---|
| Microsoft Entra roles | Directory administration | Tenant/directory unit where supported | Azure resource permissions |
| Azure RBAC | Azure Resource Manager control actions and supported data actions | Management group, subscription, resource group, resource | Application-specific authorization |
| Service data-plane authorization | Access to service data | Blob/container, Key Vault object, database | Azure management-plane permissions |
| Application roles/claims | Behavior inside an application | Application/API | Resource management |
| AD DS groups/access control lists (ACLs) | On-premises/domain-joined resources | Domain, organizational unit (OU), file ACL, application | Azure resource management |

Azure RBAC assignment = security principal + role definition + scope. Prefer the narrowest practical scope and a built-in role that meets the requirement. Use groups instead of repeated user assignments. Deny assignments are generally system-managed and are not the normal design tool.

For on-premises resources, identify the actual authorization system. Microsoft Entra authentication alone does not convert New Technology File System (NTFS), LDAP, Kerberos, or legacy application authorization into Azure RBAC. Use synchronized identities, AD DS, Microsoft Entra Domain Services, application federation, or modernization according to protocol compatibility.

### Authorizing access to on-premises resources

| Requirement | Direction |
|---|---|
| Existing domain-joined file/app resource with Kerberos/NTFS authorization | Retain AD DS groups/ACLs and synchronize the required identities; design domain connectivity and availability |
| Publish an internal web application without opening inbound firewall access | Microsoft Entra application proxy with Entra preauthentication where application/protocol support fits |
| Web application needs integrated Windows authentication behind application proxy | Validate Kerberos constrained delegation, connector identity, SPNs, and application compatibility |
| Legacy LDAP/Kerberos workload moved to Azure without self-managed domain controllers | Evaluate Microsoft Entra Domain Services; validate schema/admin/protocol limitations |
| Application can modernize | Use Entra OAuth 2.0, OpenID Connect (OIDC), or Security Assertion Markup Language (SAML) tokens and application roles/claims instead of extending legacy network trust |

Authentication at the Entra edge and authorization inside the legacy resource remain separate. Application Proxy is designed for supported web applications; it is not a universal network tunnel for arbitrary protocols.

## Workload identities

| Option | Credential lifecycle | Azure resource attachment | Best fit | Constraint |
|---|---|---|---|---|
| System-assigned managed identity | Azure manages it; deleted with resource | One identity tied to one resource | Simple one-resource lifecycle | Cannot be shared independently |
| User-assigned managed identity | Azure manages it independently | Reusable across supported resources | Shared permissions or identity survives resource replacement | Lifecycle and assignment must be governed |
| Service principal with certificate | Customer manages certificate rotation | Independent application identity | Workloads outside supported managed-identity hosts | Credential remains an operational responsibility |
| Service principal with secret | Customer manages secret | Independent | Compatibility fallback | Highest leakage/rotation risk; avoid when federation/certificate/managed identity works |
| Workload identity federation | No stored application secret; trusts external token issuer | App registration or managed identity federation scenario | Continuous integration and continuous delivery (CI/CD), Kubernetes, GitHub, or external workload | Trust configuration and issuer claims must be tightly scoped |

Decision rule: **If an Azure-hosted service supports managed identity, start there.** Use a service principal only where the identity must exist independently or the host cannot use managed identity.

## Secrets, keys, and certificates

Azure Key Vault centralizes protected objects and audit/access controls. A hardware security module (HSM) protects cryptographic keys in tamper-resistant hardware.

| Object | Use |
|---|---|
| Secret | Password, connection string, token, or opaque value |
| Key | Cryptographic operations where the key should be controlled and not exposed as application configuration |
| Certificate | Certificate lifecycle plus associated key/material |
| Azure Key Vault Managed HSM | Single-tenant, highly controlled HSM-backed key service for supported key-management requirements |

Architecture rules:

- Prefer managed identity to retrieve a secret; never embed vault credentials in code.
- Prefer Azure RBAC permission model for consistent control where it meets the scenario; do not mix access-policy and RBAC mental models.
- Use soft delete and purge protection according to recovery/compliance requirements.
- Use private endpoints and disable public access when isolation requires it; plan private DNS.
- Separate vaults when administrative, application, region, lifecycle, or blast-radius boundaries require it.
- Rotation requires consumer coordination. A secret stored in Key Vault is not automatically rotated in every dependent application.
- Key Vault availability is regional. Cross-region workloads need a deliberate vault/data replication and application failover design; do not assume object replication between vaults.

## Identity governance

| Requirement | Service/capability |
|---|---|
| Just-in-time privileged role activation | Microsoft Entra Privileged Identity Management (PIM) |
| Periodically recertify users, groups, apps, roles, or packages | Access reviews |
| Request/approval/expiration bundle for resources | Entitlement management access packages |
| Detect risky users/sign-ins and apply risk policy | Microsoft Entra ID Protection + Conditional Access |
| Govern application permission consent | Consent policies, admin consent workflow, application governance controls as required |

PIM reduces standing privilege; it does not eliminate the need for least privilege, logging, emergency access, and access reviews. Access reviews decide whether access should continue; Conditional Access decides whether a sign-in meeting current conditions is allowed.

## When not to choose

- Do not use Entra roles to grant access to a VM, storage account, or subscription.
- Do not use Azure RBAC to grant directory administration.
- Do not store application configuration values that are not secrets in Key Vault merely because it is secure; use App Configuration for centralized non-secret settings and feature flags.
- Do not deploy federation when PHS or PTA meets the mandatory requirements.
- Do not use a client secret when managed identity or workload identity federation works.
- Do not treat Microsoft Entra Domain Services as a writable replica or full replacement for every AD DS administrative feature.

## Common Trap

- Authentication and authorization are separate decisions.
- A Contributor can manage resources but cannot grant Azure RBAC access unless separately authorized for role assignments.
- Managed identity removes application credential handling; it does not grant permission automatically.
- Conditional Access protects sign-in based on signals; Azure RBAC determines allowed Azure actions after sign-in.
- Key Vault protects secrets, keys, and certificates; it is not a general configuration service.
- B2B collaboration is for external users accessing workforce resources. External ID external tenants address CIAM for customer-facing applications.

Official references: [Microsoft Entra architecture](https://learn.microsoft.com/en-us/entra/architecture/architecture), [Hybrid identity authentication methods](https://learn.microsoft.com/en-us/entra/identity/hybrid/connect/choose-ad-authn), [Managed identities](https://learn.microsoft.com/en-us/entra/identity/managed-identities-azure-resources/overview), [Azure RBAC overview](https://learn.microsoft.com/en-us/azure/role-based-access-control/overview), [Microsoft Entra External ID](https://learn.microsoft.com/en-us/entra/external-id/external-identities-overview), [Key Vault overview](https://learn.microsoft.com/en-us/azure/key-vault/general/overview).

---

## Navigation

| Previous | Home | Next |
|---|---|---|
| [← Logging and monitoring](logging_and_monitoring.md) | [Domain home](README.md) | [Governance and identity governance →](governance_and_identity_governance.md) |
