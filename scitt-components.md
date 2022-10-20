# SCITT Components

SCITT provides a set of components enabling Supply Chain, Integrity, Transparency and Trust. 
(See [What Is SCITT][WHAT_IS_SCITT])
But what are the scenarios that SCITT enables?
This collection of docs will describe the core components of SCITT, and how it can be enabled and extended to support a breadth of use cases.

As SCITT is in active design through the [IETF SCITT working group][SCITT_IETF_WG], these documents aim to facilitating various design discussions, based on a set of SCITT primitives and scenarios that benefit from the SCITT primitives.

## Table of Contents

The following is a list of documents we'll round out to enable discussions:

- [What is SCITT][WHAT_IS_SCITT]: a quick overview
- [One or Many Instances of SCITT](./scitt-components/one-or-many-instances.md): design goals and philosophy
- [Original and Updated Claims & Evidence](scitt-components/categories-of-claims-evidence.md): How SCITT supports a continual stream of updates
- [Indexing Content Types](scitt-components/scitt-indexing.md): How SCITT is extensible to enable new, evolving content types
- [RBAC](scitt-components/scitt-rbac.md): How SCITT enables limited access
- [Claims and Endorsement Format](scitt-components/claim-endorsement-spec.md): Discussion why SCITT needs a simplified format for the claims, with links to the evidence
- Scenarios SCITT Enables
  - [SBOM](scenarios/sbom.md): Evidence, initiated at creation time
  - [Revocation](scenarios/redirection-revocation.md): Claims that are continual updated as info of the product is learned.
  - [Fuel Pump Certification](scenarios/fuel-pump.md): hardware scenario where multiple instances of a product (the serial number) have individual claims and evidence

[SCITT_IETF_WG]:  https://datatracker.ietf.org/group/scitt/
[WHAT_IS_SCITT]:  https://ietf-scitt.github.io/scitt-web/