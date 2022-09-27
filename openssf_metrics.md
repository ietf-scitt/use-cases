# OpenSSF Metrics

Collection of metric / Alpha-Omega data into shared DB.

## Misc. Notes

Mike from OpenSSF has been thinking about SCITT as a schema
and rules on how one would assert facts, weither it's
confidential compute or traditional permissions is impelmenetation details.

- TODO
  - Align with Software Supply Chain Artifact Examples use case
    - https://github.com/ietf-scitt/use-cases/pull/17/files
  - As consumer, how can I discover that fact and trust that it's accruate
  - Could imagine a world where things like Scorecard express the data as as SCITT attestation
  - You go and query that (source code data mining data or vuln data) store (via DID resolution)
    and you say tell me everythig you know about foo and you get it all back.
    - Addresses OpenSSF/Christine's: Looking at trying to connect all the different data sources
  - Until we have an implementation with Web5 (https://github.com/TBD54566975) that's at at least beta, we could expore what that looks like.
  - Open Architecture: need to track usages / `reuse` of contexts `ictx`, `nctx`, etc. with
    something predeclared, aka at runtime if your `Operation` data structure doesn't
    allowlist your usage of it you can pass it to a subflow for reuse.
    This allows us to use the format within our orchrestration and for static analysis
    because we can use this same format to describe the trust boundry proeprties that
    other domain sepcific represenatations of architecture have, for instance we could
    if we were doing and Open Architecture (OA) Intermediate Representation (IR) for
    and ELF file we might note that the input network context is not reused from
    the top level system context. Where as if we did an OA IR for Python code we
    would say that the input network is reused from the top level system context
    (it has access to that memory region, whereas when you launch and ELF you look
    access to the parents memory region, typically).

We care about data provenance. This provenance could be for example on
inference derived from provenance from training data and model training
env and config. This will allow us to ensure the prioritizer make
decisions based on Sprit of the law / aka intent based policy derived from
[Trinity of Static Analysis, Dynamic Analysis, and Intent](https://github.com/intel/dffml/tree/alice/docs/tutorials/rolling_alice/0000_architecting_alice#entity-analysis-trinity).

Living Threat Model threats, mitigations, trust boundaries as initial data
set for cross domain conceptual mapping of the the trinity to build pyramid
of thought alignment to strategic principles.

- One of our strategic plans / principles might say
  - "We must be able to trust the sources of all input data used for all
    model training was done from research studies with these ethical
    certifications"
- This allows us to write policies (Open Policy Agent to JSON to DID/VC/SCITT
  translation/application exploration still in progress) for the organizations
  we form and apply them as overlays to flows we execute where context appropriate.
  These overlaid flows define the trusted parties within that context as applicable
  to the active organizational policies as applicable to the top level system context.
- The policy associated with the principle that consumes the overlaid trust
  attestations we will implement and LTM auditor for which checks
  the SCITT provenance information associated with the operation implementations and
  the operation implementation network, input network, etc. within the orchestrator
  trust boundary 

  TODO 

- References
  - https://github.com/intel/dffml/blob/alice/docs/arch/0009-Open-Architecture.rst
  - https://github.com/intel/dffml/blob/alice/docs/arch/0008-Manifest.md
    - https://github.com/intel/dffml/tree/alice/docs/tutorials/rolling_alice
  - https://datatracker.ietf.org/doc/html/draft-birkholz-scitt-architecture
  - https://www.w3.org/2022/07/pressrelease-did-rec.html.en
  - https://docs.microsoft.com/en-us/azure/confidential-ledger/architecture
  - Similar work to address
    - https://docs.google.com/presentation/d/1WF4dsJiwR6URWPgn1aiHAE3iLVl-oGP4SJRWFpcOlao/edit#slide=id.g14078b5bab0_0_517
