# Hardware / Microelectronics Use Case

## Scope
This document aims to confirm that the SCITT building blocks and threat model satisfy the use case requirements. It is not intended to be all-encompassing across all aspects of the hardware supply chain. Instead, it introduces the topic and highlights unique properties that warrant investigation in the working group and may influence the final architecture document. Ultimately, implementations will make final design decisions (e.g., storage/network architectures, standards/policies, artifacts, signatures, endorsements, claims) based on their mission needs while meeting the interoperability objectives of SCITT.

* **Focused Use Case**: This use case considers counterfeit microelectronics, a subset of the generalized use case. We expect this focused use case to represent hardware requirements for the generalized use case adequately. Afterward, we describe use case alignment to SCITT across architecture, terminology, and the threat model.
* **Generalized Use Case**: This use case provides an industry perspective on applying SCITT to hardware/microelectronics supply chains and introduces some challenges encompassing the complex end-to-end lifecycle.

## Focused Use Case: Counterfeit Microelectronics
A counterfeit microelectronic device is an unauthorized reproduction, substitution, or alteration that has been knowingly mismarked, misidentified, or misrepresented as an authentic, unmodified device. For example, used microelectronics may be represented as new and have falsely identified grade, serial number, lot number, date code, or performance characteristics. Ideally, one should source devices from the original manufacturer or a source with the express written authority of the original manufacturer or current design activity, including an authorized aftermarket manufacturer. Legacy devices present a unique challenge given the end-of-life status with the original manufacturer and limited if any, new-old-stock inventories. Existing standardized practices and methods, such as those from SAE International [G-19 Counterfeit Electronics Parts Committee](https://www.sae.org/works/committeeHome.do?comtID=TEAG19) and [G-21 Counterfeit Materiel Commitee](https://www.sae.org/works/committeeHome.do?comtID=TEAG21), require supply chain traceability as a fundamental solution element.

## SCITT Alignment

### Building Blocks
* **Architecture and Terminology**: This use case involves highly complex supply chains to include upstream supply chain data flows (i.e., promotion of claims) that may not be considered in traditional producer-to-consumer models.
* **Information and Interaction Models**: This use case highlights data flows and interactions necessary to support rich data artifacts in supplier-controlled storage with a separate and comparatively lightweight transparency registry. Given data privacy and potential air-gap implementations, this use case will also explore the impacts on transparency service policies, governance, and federation.
* **Countersigning Format in Support of Transparency Services**: This use case does not present any unique requirements.
* **Generic Protocol Bindings**: This use case will explore API gateways or private API endpoints to support diverse network/storage handling requirements.

### Threat Model
* **Claim Authentication and Transparency**: The generic threat model sufficiently examines this area for the hardware use case.
* **Confidentiality and Privacy**: The generic threat model describes implementation-specific flexibility, which will support the hardware use case in opaque cryptographic commitments to protect the confidentiality and privacy of supplier claims and queries.
* **Cryptographic Assumptions**: The generic threat model sufficiently examines this area for the hardware use case.
* **Transparency Service Clients**: As mentioned in the generic threat model, the trust in TS clients is implementation-specific. One approach may be establishing a trusted supplier network in the hardware domain consisting of a shared governance model with restrictive registration policies. Still, the diversity of the supply chain will require flexibility to add or remove TS clients as the level of trust changes.
* **Identity**: In the case of credential compromise, the revocation principles from the generic threat model apply. Unlike software, a rollback to claims registered before the compromise becomes problematic in the case of hardware manufacturing. There are unique policy and governance implications concerning identity and revocation to be explored in the hardware use case.

## Generalized Use Case

### Opportunity
The security evaluation of hardware components and fully integrated microelectronic systems are foundational to delivering trustworthy, resilient, and safe products/services to meet confidentiality and integrity requirements. In the current industry landscape, one of the most significant barriers to this vision has been the inability to provide valuable data for analytics and security insights while protecting data privacy for suppliers. There is a need for a platform architecture that will allow consumers to know that suppliers maintained appropriate security practices without requiring access to proprietary intellectual property. SCITT-enabled products and analytics solutions will aid in preventing and detecting supply chain attacks across the entire lifecycle of microelectronics (e.g., IP, design, manufacturing, provisioning, assembly, shipping, operation, decommissioning) while prioritizing data privacy.

### Problem Background
The globalization of the semiconductor industry has led to the introduction of numerous untrusted entities into different stages of a device’s lifecycle. This has made it challenging to ensure the security of semiconductor devices across their supply chain lifecycle. Supply chain attacks can significantly impact the systems they target and can be very difficult to mitigate. Companies need to work together to improve current approaches and provide better validation of exchanged goods. As illustrated in **Figure 1**, a supply chain attack may leverage one or more lifecycle stages and directly or indirectly target the component.

![](https://i.imgur.com/tYpJYQx.png) 
###### **Figure 1**: Hardware Supply Chain Attack Vectors

Artifacts may be collected throughout the lifecycle improving hardware supply chain security and mitigating threats. For context, **Figure 2** describes example supply chain security artifacts at each major stage that may augment traditional logistics, qualification, and supply chain risk management data. Standards bodies and industry consortiums are actively defining what artifacts are beneficial, what level of detail is required based on the customer mission, and how best to extract these data across the diverse value chain.

![](https://i.imgur.com/dgeLeFo.png)
###### **Figure 2**: Example Supply Chain Security Lifecycle Artifacts

### Notional Implementation
A SCITT implementation will establish that it is possible to create a secure, distributed transparency service (TS) registry containing the complete history of a commercial assembly while maintaining strict privacy of the source supplier data, as illustrated in **Figure 3**. Participating actors will not receive any new supply chain data outside of what is explicitly published to the TS registry and allowed by the suppliers via tailorable identity services (i.e., authentication and authorization). Thus, the SCITT architecture will remove the requirement for complex data-sharing agreements. Each supplier will operate within logical or physical isolation for interaction with the TS service. The proprietary artifacts require considerable data resources, and suppliers should apply OCI Registries as a generic artifact storage solution, separate from the relatively lightweight claims held in the TS registry. Custom data governance will control the labeling and sharing of data with downstream supplier registries and auditors.

![](https://i.imgur.com/TUwNddI.png)
###### **Figure 3**: Supply Chain Security Architecture that Protects Privacy

Standards bodies and customer profiles will define the diverse requirements to verify and validate a specific product and customer value chain's functionality, trustworthiness, safety, security, etc. These requirements will determine policies to apply, artifacts to collect, and endorsements needed across a range of systems, from simplistic consumer electronics (e.g., alarm clocks) to intricate cyber-physical systems in critical infrastructure (e.g., nuclear power plants).

An endorsement (i.e., a statement about the artifact that is asserted) could fit at least two scenarios: Supplier self-endorsement and a third-party endorsement (e.g., auditor under an NDA). Many security approaches are possible with an auditor, including only sharing metadata from artifacts, requiring analysis in the supplier's environment, following security policies through digital contracts, etc. An auditor evaluates the artifacts against a compliance standard and then creates a signed claim that is notarized and stored in the TS registry. An end customer would then have the assurance that the delivered product/service meets a compliance standard via the transparent claim. How the product/service meets said standard (e.g., justification, supporting artifacts) would remain private to the supplier/trusted auditor but may be disclosed should the supplier wish. Downstream suppliers and customers may consume, fork, and create new TS registry entries to encompass the hardware component's or microelectronics system's lifecycle management requirements.
