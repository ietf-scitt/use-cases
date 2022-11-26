# OpenSSF Metrics

> Pull Request Review (WIP): https://github.com/ietf-scitt/use-cases/pull/18

Collection of metric data into shared (crowdsourcable) DB. There are many repos
to search, we want to enable self reporting and granularity as applicable to
ad-hoc formed policy as desired by end-user.

- NVD API style as first way to distribute VEX.
  - ActivityPub publish as well
   - Websub for new notifications? Look up how Mastodon does.
- Added CLI command `alice threats vulns serve nvdstyle`
  - https://github.com/intel/dffml/commit/cb2c09ead795ba0046cb5911bcd6e939419058d8
  - https://github.com/intel/dffml/blob/4101595a800e74f57cec5537ea2c65680135b71a/entities/alice/alice/threats/vulns/serve/nvdstyle.py#L1-L241
- The Open Architecture (Alice) sits at the interesction of CI/CD, Security, and AI/ML.
  - We metion Alice here as a follow on who's development sees this use case as critical (comms ;)
- Think cross between review system (SCITT as the proof, TDB on identity preknown at this point, OpenSSF members stream 8 vuln sharing CCF ledger)
- https://scitt.io/distributing-with-oci-scitt.html
- https://www.darkreading.com/dr-tech/cybersecurity-nutrition-labels-still-a-work-in-progress
  - https://www.whitehouse.gov/briefing-room/statements-releases/2022/10/20/statement-by-nsc-spokesperson-adrienne-watson-on-the-biden-harris-administrations-effort-to-secure-household-internet-enabled-devices/
    - > Yesterday, the White House convened leaders from the private sector, academic institutions, and the U.S. Government to advance a national cybersecurity labeling program for Internet-of-Things (IoT) devices. The Biden-Harris Administration has made it a priority to strengthen our nation’s cybersecurity, and a key part of that effort is ensuring the devices that have become a commonplace in the average American household – like baby monitors or smart home appliances – are protected from cyber threats. A labeling program to secure such devices would provide American consumers with the peace of mind that the technology being brought into their homes is safe, and incentivize manufacturers to meet higher cybersecurity standards and retailers to market secure devices.
      >
      > Yesterday’s dialogue focused on how to best implement a national cybersecurity labeling program, drive improved security standards for Internet-enabled devices, and generate a globally recognized label. Government and industry leaders discussed the importance of a trusted program to increase security across consumer devices that connect to the Internet by equipping devices with easily recognized labels to help consumers make more informed cybersecurity choices (e.g., an “EnergyStar” for cyber). These conversations build on the foundational work that has been pioneered by the private sector and the National Institute of Standards and Technology (NIST) to help build more secure Internet-connected devices. It also follows President Biden’s Executive Order on Improving the Nation’s Cybersecurity, which highlighted the need for improved IoT security and tasked NIST, in partnership with the Federal Trade Commission, to advance improved cybersecurity standards and standardized product labels for these devices.
- https://csrc.nist.gov/publications/detail/white-paper/2022/11/09/implementing-a-risk-based-approach-to-devsecops/final
  - > DevOps brings together software development and operations to shorten development cycles, allow organizations to be agile, and maintain the pace of innovation while taking advantage of cloud-native technology and practices. Industry and government have fully embraced and are rapidly implementing these practices to develop and deploy software in operational environments, often without a full understanding and consideration of security. Also, most software today relies on one or more third-party components, yet organizations often have little or no visibility into and understanding of how these components are developed, integrated, deployed, and maintained, as well as the practices used to ensure the components’ security. To help improve the security of DevOps practices, the NCCoE is planning a DevSecOps project that will focus initially on developing and documenting an applied risk-based approach and recommendations for secure DevOps and software supply chain practices consistent with the Secure Software Development Framework (SSDF), Cybersecurity Supply Chain Risk Management (C-SCRM), and other NIST, government, and industry guidance. This project will apply these DevSecOps practices in proof-of-concept use case scenarios that will each be specific to a technology, programming language, and industry sector. Both closed source (proprietary) and open source technology will be used to demonstrate the use cases. This project will result in a freely available NIST Cybersecurity Practice Guide.
- https://www.intel.com/content/www/us/en/newsroom/news/2022-intel-innovation-day-2-livestream-replay.html#gs.djq36o
  - Similar to the software labeling, with Alice we are trying to cross these streams
    - Datasheets for Datasets
      - https://arxiv.org/abs/1803.09010
      - > The machine learning community currently has no standardized process for documenting datasets, which can lead to severe consequences in high-stakes domains. To address this gap, we propose datasheets for datasets. In the electronics industry, every component, no matter how simple or complex, is accompanied with a datasheet that describes its operating characteristics, test results, recommended uses, and other information. By analogy, we propose that every dataset be accompanied with a datasheet that documents its motivation, composition, collection process, recommended uses, and so on. Datasheets for datasets will facilitate better communication between dataset creators and dataset consumers, and encourage the machine learning community to prioritize transparency and accountability.

> Side from Andrew Ng's Intel Innovation 2022 Luminary Keynote
> Source: https://www.intel.com/content/www/us/en/newsroom/news/2022-intel-innovation-day-2-livestream-replay.html#gs.iex8mr
> ![image](https://user-images.githubusercontent.com/5950433/193330714-4bcceea4-4402-468f-82a9-51882939452c.png)

- Possible alignment with Andrew's "Data-Centric AI"
 - is the discipline of systematically engineering the data used to build an AI system
   - This is what we're doing with Alice
- Possible alignment with Andrew's "The iterative process of ML development"
  - https://github.com/intel/dffml/tree/alice/docs/tutorials/rolling_alice/0000_architecting_alice#entity-analysis-trinity
  - Intent / Train model
    - Establish correlations between threat model intent and collected data / errors (telemetry or static analysis, policy, failures)
  - Dynamic analysis / Improve data
    - We tweak the code to make it do different things to see different data. The application of overlays. Think over time.
  - Static / Error analysis
    - There might be async debug initiated here but this maps pretty nicely conceptually since we'd think of this as a static process, we already have some errors to analyze if we're at this step.

*Gif of Alice on roller skates throwing a bowling ball which is a software vuln, strike, she frontflips throwing knife style throws the pins into pull requests. We zoom out and see her just doing this over and over again around the Entity Analysis Trinity. Intent/LTM is where the throwing board is. Bowling alley is static analysis and the end of the bowling ally where she frontflips over (through hoop of CI/CD fire?) is where she pics up the pins and throws them as pull request (titles and numbers maybe, pulls/1401 style maybe?) knives into the board at the top which is the LTM and codebase. Then from top, LTM to static analysis where bowling alley starts shes in the lab, cooking up the vuln or maybe out looking for it. Or maybe refactoring after pull requests!*

![Entity Analysis Trinity](https://user-images.githubusercontent.com/5950433/188203911-3586e1af-a1f6-434a-8a9a-a1795d7a7ca3.svg)

## Misc. Notes

Mike from OpenSSF has been thinking about SCITT as a schema
and rules on how one would assert facts, weither it's
confidential compute or traditional permissions is impelmenetation details.

- TODO
  - Go over `.github/workflows/alice_shouldi_contribute.yml` which is called as reusable
    workflow using SLSA demos as conceptual upstream.
    - This gives us metric collection with overlays applied to input network which log
      data provenance later used via policy referenced in a Living Threat Model
      of a downstream end user facing application. The use case document will cover
      how we effectivly self notarize the data provenance assisted by the OIDC token
      issused to the workflow from GitHub.
  - Align with Software Supply Chain Artifact Examples use case
    - https://github.com/ietf-scitt/use-cases/pull/17/files
  - As CI/CD velocity increases, we approach a similar threat model to distributed
    compute, address here.
    - https://github.com/intel/dffml/blob/alice/docs/tutorials/rolling_alice/0000_forward.md#supply-chain-security
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
- References
  - https://scitt.io/distributing-with-oci-registries.html
    - https://github.com/ucan-wg/invocation/pull/1/files#diff-b335630551682c19a781afebcf4d07bf978fb1f8ac04c6bf87428ed5106870f5R85
  - https://github.com/intel/dffml/blob/alice/docs/arch/0009-Open-Architecture.rst
  - https://github.com/intel/dffml/blob/alice/docs/arch/0008-Manifest.md
    - https://github.com/intel/dffml/tree/alice/docs/tutorials/rolling_alice
  - [2022-09-29 IETF SCITT Technical Meeting](https://github.com/intel/dffml/discussions/1406#discussioncomment-3763647)
  - https://github.com/transmute-industries/did-eqt/blob/main/docs/did-eqt-opa-primer.md#securing-did-method-operations-with-opa
  - https://datatracker.ietf.org/doc/html/draft-birkholz-scitt-architecture
  - https://www.w3.org/2022/07/pressrelease-did-rec.html.en
  - https://docs.microsoft.com/en-us/azure/confidential-ledger/architecture
    - In search of more easy options to faciliate public/private hybrid chains
      of supply chain data. ideally OSS (SSI Service?).
  - Similar work to address
    - https://docs.google.com/presentation/d/1WF4dsJiwR6URWPgn1aiHAE3iLVl-oGP4SJRWFpcOlao/edit#slide=id.g14078b5bab0_0_517
- We first issue the verifiable credentials, with a reference to the rest of the hashlinks (or whatever the CBOR style thing is)
  - Open Source Decentralized Identifiers and Verifiable Credentials Infrastructure and Tooling - verifiable-data/Hashlink.ts at c80cab9abe4db478add16b14837ba9a3afc3a70f
  - Then we hashlink that, and that's the top level ref. effectivly the evolution of the zephyr.stripped shasum patchset
  - https://github.com/zephyrproject-rtos/zephyr/pull/51954
- Future
  - As a follow on to the OpenSSF Metrics use case document and
    [Living Threat Models are better than Dead Threat Models](https://www.youtube.com/watch?v=TMlC_iAK3Rg&list=PLtzAOVTpO2jYt71umwc-ze6OmwwCIMnLw)
    [Rolling Alice: Volume 1: Coach Alice: Chapter 1: Down the Dependency Rabbit-Hole Again](https://github.com/intel/dffml/blob/alice/docs/tutorials/rolling_alice/0001_coach_alice/0001_down_the_dependency_rabbit_hole_again.md)
    will cover how we identify and query provenance on dependencies where caching
    on data flow execution is assisted via quering public SCITT infrastructure
    and sourcing cached state from trustworthy parties.
  - Leveraging our restoration of cached state from trustworthy parties and
    LTM policy we can measure alignment of ML model's used within BOM components
    so as to report conceptual alignment with entity strategic plans / principles.
    - We care about data provenance. This provenance could be for example on
      inference derived from provenance from training data and model training
      env and config. This will allow us to ensure the prioritizer make
      decisions based on Sprit of the law / aka intent based policy derived from
      [Trinity of Static Analysis, Dynamic Analysis, and Intent](https://github.com/intel/dffml/tree/alice/docs/tutorials/rolling_alice/0000_architecting_alice#entity-analysis-trinity).
    - Living Threat Model threats, mitigations, trust boundaries as initial data
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
      trust boundary.
