# Indexing Specific Content Types

> TODO:
> - SCITT must provide some level of indexing to find the most basic information (does artifact _**a**_ have claim _**b**_, but only return claims if it was submitted by identities _**x,y,z**_)
> - How will SCITT support new content types, who's evidence is stored in associated storage? Some SCITT instances will support SPDX, others CycloneDX. What happens when a new format appears (VEX as an example). Can the "vex team" implement a new indexer that any SCITT instance can add? Can SCITT provide an extensibility model enabling this pattern?

> Assumptions:
> - SCITT doesn't store evidence on the ledger
> - SCITT has associated storage, or at least a PURL to find the evidence on some storage system
> - SCITT should provide query capabilities to query ont he info in the SCITT ledger, with extensibility to AND information that is stored externally. The returned results would provide PURLs (assumed to be a purl) to the location of the storage. This enables various subsystems to store content types, with SCITT adding value as a central indexing system, without owning the content