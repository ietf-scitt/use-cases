# SCITT Components

SCITT provides a set of components enabling Supply Chain, Integrity, Transparency and Trust. 
(see [What Is SCITT][WHAT_IS_SCITT])
But what are the scenarios, projects and products that SCITT enables?
This collection of docs will describe the core components of SCITT, and how it can be enabled and extended to support a breadth of use cases, enabling new and existing products and services.

As SCITT is in active design through the [IETF SCITT working group][SCITT_IETF_WG], these documents aim to facilitate various design discussions, based on a set of SCITT primitives and scenarios.

## Table of Contents

The following is a list of documents we'll round out to enable discussions:

- **Getting Started**
  - [What is SCITT][WHAT_IS_SCITT]: a quick overview to get started
  - [What are Verifiable Identities](scitt-components/verifiable-identities.md): The importance of every piece of info in a supply chain to be backed by a verifiable identity
  - [Extending Existing Projects, Products and Services with SCITT](scenarios/extending-existing-services.md)
  - [Types of Claims and Evidence Supported by SCITT](scitt-components/types-claims-evidence.md)
  - [Original and Updated Claims & Evidence](scitt-components/categories-of-claims-evidence.md): How SCITT supports a continual stream of updates
  - [Importance of Detached Signatures, Claims and Evidence](scitt-components/detached-artifacts.md)
  - [eNotary & Policy](assets/enotary.svg): The role of the eNotary and ingestion policy
- **Scenarios SCITT Enables**
  - [Canonical Customer Scenarios](canonical-customers.md): Wabbit Networks and ACME Rockets
  - **Software Workflow**
    - [Building and Consuming Dependencies](scenarios/building-dependencies.md)
    - [Building Apps](scenarios/building.md)
    - [SBOM](scenarios/sbom.md): Evidence, initiated at creation time
    - [Promoting from Dev, Test to Production](scenarios/promoting-dev-test-prod.md)
    - [Publishing Updates](scenarios/publishing-updates.md): how to communicate a new version is available, and does it supersede a previous version?
    - [Revocation](scenarios/redirection-revocation.md): Claims that are continual updated as info of the product is learned.
  - **Hardware Scenarios**
    - [Fuel Pump Certification](scenarios/fuel-pump.md): hardware scenario where multiple instances of a product (the serial number) have individual claims and evidence
- **Design Discussions**: Areas for us drill in, to enable the above scenarios
  - [Claims and Endorsement Format](scitt-components/claim-endorsement-spec.md): Discussion why SCITT needs a simplified format for the claims, with links to the evidence
  - [One or Many Instances of SCITT](./scitt-components/one-or-many-instances.md): design goals and philosophy
  - [RBAC](scitt-components/scitt-rbac.md): How SCITT enables limited access
  - [Indexing Content Types](scitt-components/scitt-indexing.md): How SCITT is extensible to enable new, evolving content types

[SCITT_IETF_WG]:  https://datatracker.ietf.org/group/scitt/
[WHAT_IS_SCITT]:  https://ietf-scitt.github.io/scitt-web/