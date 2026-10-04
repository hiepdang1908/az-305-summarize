# AZ-305 Knowledge-First Architecture Guide

A structured, knowledge-first study repository for **AZ-305: Designing Microsoft Azure Infrastructure Solutions**. It follows the current Microsoft skills measured and focuses on architecture decisions, service trade-offs, constraints, dependencies, failure modes, cost, and operations.

## Purpose and scope

This repository covers the current AZ-305 design objectives. It is an architecture reference, not a deployment tutorial.

It focuses on:

- Selecting services from workload requirements
- Explaining why a plausible alternative does not fit
- Availability, durability, recovery time objective (RTO), recovery point objective (RPO), backup, and disaster recovery
- Identity, governance, networking, data, compute, integration, and migration boundaries
- Security, compatibility, operational responsibility, and qualitative cost
- Cross-domain reasoning and high-yield recall

It intentionally avoids:

- Labs, CLI/PowerShell walkthroughs, and infrastructure-as-code tutorials
- Exam dumps, copied questions, reconstructed questions, and answer keys
- Marketing content and volatile price/service-level agreement (SLA)/quota memorization
- Basic AZ-104 detail unless it changes an architecture decision

## Source policy

Current Microsoft Learn and Azure documentation are the technical source of truth. The maintained source index is [sources/official-sources.md](sources/official-sources.md).

Files under `example/` were used only to find recurring topics, confused services, and knowledge gaps. No practice question or answer was copied or reconstructed. When practice material conflicts with Microsoft documentation, Microsoft documentation wins. See [source validation notes](sources/source-validation-notes.md).

## Repository learning path

1. [Azure architect design prerequisites](AZ_305_architect_prerequisites/README.md)
2. [Master Mental Map](AZ-305_MASTER_MENTAL_MAP.md)
3. [Objective Map](AZ-305_OBJECTIVE_MAP.md)
4. [Identity, governance, and monitoring](Design_identity_governance_and_monitoring/README.md)
5. [Data storage](Design_data_storage_solutions/README.md)
6. [Business continuity](Design_business_continuity/README.md)
7. [Infrastructure](Design_infrastructure_solutions/README.md)
8. [High-Yield Recall](AZ-305_HIGH_YIELD_RECALL.md)

Quick reference: [AZ-305 abbreviation glossary](AZ-305_GLOSSARY.md).

Microsoft Learn organizes preparation into **five learning paths**: one architect-prerequisite path and four domain paths. The exam blueprint scores only the **four domains** below. The prerequisite path builds shared architecture vocabulary; it is not a fifth scored domain and has no exam weighting.

## Current blueprint

Skills measured as of April 17, 2026:

| Official domain | Weight | Repository location |
|---|---:|---|
| Design identity, governance, and monitoring solutions | 25–30% | `Design_identity_governance_and_monitoring/` |
| Design data storage solutions | 20–25% | `Design_data_storage_solutions/` |
| Design business continuity solutions | 15–20% | `Design_business_continuity/` |
| Design infrastructure solutions | 30–35% | `Design_infrastructure_solutions/` |

The [Objective Map](AZ-305_OBJECTIVE_MAP.md) maps all 49 published objective bullets to substantive sections.

Repository coverage uses two independent measures:

- Exam blueprint objectives: **49/49**
- Microsoft Learn preparation paths: **5/5**

## Repository structure

```text
az-305-summarize/
├── README.md
├── AZ-305_MASTER_MENTAL_MAP.md
├── AZ-305_OBJECTIVE_MAP.md
├── AZ-305_HIGH_YIELD_RECALL.md
├── AZ-305_GLOSSARY.md
├── AZ_305_architect_prerequisites/
├── Design_identity_governance_and_monitoring/
├── Design_data_storage_solutions/
├── Design_business_continuity/
├── Design_infrastructure_solutions/
├── sources/
└── example/
```

## Core decision discipline

Rank scenario constraints in this order when applicable:

1. Business outcome and mandatory functional requirement
2. Security, identity, compliance, and prohibited designs
3. Availability, RTO, and RPO
4. Data model, consistency, durability, and residency
5. Network location, connectivity, and exposure
6. Scalability, latency, throughput, and performance
7. Compatibility and migration constraints
8. Operational responsibility and team capability
9. Cost constraint
10. Recommended solution and explicit reasons competing solutions fail

The lowest-cost service is not correct if it misses a mandatory requirement. The most feature-rich service is not correct when its complexity is unjustified.

## Limitations

Azure changes continuously. Before production use, validate current Microsoft documentation for:

- Regional and availability-zone support
- Product lifecycle, preview/generally available (GA) state, and naming
- Stock keeping unit (SKU)/edition feature combinations
- Quotas, limits, pricing, and SLAs
- Migration source-target support
- Replication, backup, failover, and private-network feature compatibility

This repository deliberately avoids volatile exact values unless they change a durable architecture decision.

## Certification disclaimer

This is an independent educational resource. It is not affiliated with, sponsored by, endorsed by, or approved by Microsoft. It contains no copied or reconstructed certification questions, no answer keys, and no confidential, remembered, or protected certification material.

---

## Navigation

| Previous | Home | Next |
|---|---|---|
| — | [AZ-305 Home](README.md) | [Architect prerequisites →](AZ_305_architect_prerequisites/README.md) |
