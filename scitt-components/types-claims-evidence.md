# SCITT: Supported Types of Claims and Evidence

The security of a supply chain must continue to evolve.
As a result, SCITT adopts a model of minimal fixed formats with full extensibility, enabling an ever evolving ecosystem of scenarios and evidence types.
Users will be able to submit verifiable claims and evidence to a SCITT instance, without concern that new evidence types may be limited.

## Claims and Queries

SCITT Claims have a minimal schema to enable stability across SCITT instances, where users can query claims, with links to optional evidence. However, the types of evidence to represent a claim is limitless.

## Evidence

The types of evidence we know today will quickly evolve. When we started the projects, SBOMs were just evolving, with two specific formats (SPDX and CycloneDX).
SCITT should not take a bet on any specific format, while specific users may.
New formats quickly evolve, such as VEX. Users must have the ability to adopt new formats, and the community must have a way develop new formats without having to focus on how they distribute or associate those formats with the supply chain projects.
As a result, a SCITT instance will support all types of evidence formats, with the option for how to query into them.

## Evidence Indexing Extensibility

All SCITT instances will have the ability to query a basic set of claims.
However, there will be concrete examples where a user will also need to query into specific evidence formats.

**Example**: What artifacts are currently deployed in production, and have the log4j component?

In the above example, the user could query the SCITT ledger for the claim `deployed-to-production:prod-a`. If the SCITT ledger simply returned all the software currently deployed, requiring a query of a separate system for their SBOM format of choice, the value quickly diminishes. If the SCITT instances supported index extensibility, someone could write the SPDX or CycloneDX indexer allowing the user to write a query that finds the production deployed projects, AND those that have log4j in their SBOMs.

## Design Discussions:

- [SCITT Indexing](scitt-indexing.md)
