---
title: Open Blockchain Intelligence Standards
description: An open community effort developing vendor-neutral standards for blockchain intelligence.
type: docs
bookToc: false
---

<section class="obis-hero">
  <h1 class="obis-title">Open Blockchain Intelligence Standards</h1>
  <p class="obis-tagline">An open community effort developing vendor-neutral standards for blockchain intelligence.</p>
</section>

Blockchain intelligence has become an established professional domain. Investigators, prosecutors, supervisors, and researchers rely on it in high-stakes settings: criminal proceedings, supervisory enforcement, and peer-reviewed research. The infrastructure the domain depends on has not kept pace. Data formats vary between platforms, the same concepts carry different names, foundational computations such as address clustering follow divergent methods, and findings produced in one tool ecosystem are difficult to verify or reuse in another.

OBIS develops open, vendor-neutral specifications to close that gap. The work is done in public, and anyone who wants to contribute can.

## About {#about}

OBIS is a community effort, not an institution. Drafts are developed in the open on GitHub. The process borrows from the [IETF](https://www.ietf.org/) and [W3C](https://www.w3.org/): public drafts, defined review windows, and public responses to review comments. The organisational form is lighter than either: there is no board, no membership tiers, and no application to join.

OBIS organises its work around concrete use cases rather than abstract layers, because the gaps that matter most appear at the boundary between organisations doing similar work with incompatible tools.

OBIS is currently unincorporated. Incorporation as a non-profit standards organisation is planned once the body of work and the contributor base warrant it. Until then the project has no legal personhood: contributions are made directly to the public specifications under the licences described on the [Licence](/about/license/) page, and the project holds no funds, assets, or contractual obligations.

OBIS is not a certification scheme, an industry lobby, or a vendor consortium. It is a body of open specifications developed in public by whoever is willing to contribute.

The need for shared standards was discussed at the 9th Global Conference on Criminal Finances and Cryptoassets and [set out](https://baselgovernance.org/resources/news/developing-blockchain-intelligence-standards-and-interoperability-a-critical-need-to-fight-financial-crime-in-the-digital-age-2879) in a subsequent contribution published by the Basel Institute on Governance. OBIS is the venue for turning that need into specifications.

### Why open {#why-open}

Openness applies to formats, vocabularies, data models, and procedures, not to intelligence. An OBIS specification defines how an attribution record is structured or what a named clustering method computes; it does not require anyone to publish which addresses belong to whom, which cases are under investigation, or how a tool selects, tunes, and combines the methods it applies. That content stays with whoever holds it. Adjacent fields already work this way: digital forensics documents its evidence-handling procedures ([ISO/IEC 27037](https://www.iso.org/standard/44381.html)) and its tool-testing methodology ([NIST CFTT](https://www.nist.gov/itl/csd/secure-systems-and-applications/computer-forensics-tool-testing-program-cftt)) without disclosing the evidence, and cyber-threat intelligence moves sensitive indicators over the open [STIX](https://docs.oasis-open.org/cti/stix/v2.1/os/stix-v2.1-os.html) and [TAXII](https://docs.oasis-open.org/cti/taxii/v2.1/os/taxii-v2.1-os.html) specifications. The procedures and formats are public; the payload is not.

In blockchain intelligence, openness is also what makes the work defensible. A court, a defence expert, or a peer reviewer can assess a finding only against a method whose definition is public. A tool that states which OBIS-defined methods it applied gives them that public baseline without disclosing its data or its proprietary refinements. A closed standard would reproduce the black-box problem that case-by-case vendor testimony already creates.

## Focus areas {#focus-areas}

Three use-case domains anchor the first work cycle. Each corresponds to a concrete interoperability problem.

<div class="obis-focus-grid">

{{< obis-card mark="§ I" title="Investigations, forensics, and attribution" >}}

Investigators work in isolated tool ecosystems with no systematic way to share findings and leads across platforms or agencies. Prosecutors find it difficult to demonstrate the reliability and validity of blockchain evidence because no standardised validation procedures exist, which complicates questions of admissibility. Courts therefore rely on case-by-case testimony from individual tool providers.

OBIS work in this area: a shared [vocabulary]({{< relref "/standards/OBIS-0002" >}}), data models and exchange formats for [address attribution]({{< relref "/standards/OBIS-0003" >}}), reference specifications for address clustering, and formats that allow transaction graphs to be shared across organisations without exposing proprietary attribution data.

{{< /obis-card >}}

{{< obis-card mark="§ II" title="Regulatory reporting and supervision" >}}

Supervisors apply inconsistent methodologies and typologies when assessing virtual-asset activity, and reporting entities maintain bespoke integrations with every supervisor and counterparty they exchange data with.

OBIS work in this area: standardised exchange formats between cryptoasset service providers (CASPs), supervisors, and other reporting entities, reducing integration cost and improving the comparability of supervisory data across jurisdictions.

{{< /obis-card >}}

{{< obis-card mark="§ III" title="Open analytics and research data" >}}

Research on blockchain data, academic and applied, is held back by the absence of shared data sets, schemas, labelling conventions, and reproducibility primitives. Each new study re-derives its data layer from raw chain history.

OBIS work in this area: schemas and conventions that let studies build on a shared data layer instead of rebuilding it from raw chain history each time.

{{< /obis-card >}}

</div>

A fourth area, compliance and sanctions screening, is **out of scope at launch**. That space is commercially crowded and already served by established vendor formats; it may be revisited in a later cycle.

## Relationship to existing work {#relationship-to-existing-work}

OBIS specifications sit alongside the existing body of work in this space rather than replacing it. OBIS does not produce policy, guidance, or interpretive commentary on any of the instruments below; it produces technical artefacts that implementations of them can build on.

- **FATF Recommendations and guidance**, in particular Recommendation 15 on virtual assets and Recommendation 16 on the Travel Rule, set the international policy framework.

- **EU instruments** including MiCA (Regulation (EU) 2023/1114), the recast Transfer of Funds Regulation (Regulation (EU) 2023/1113), and EBA reporting frameworks define obligations and reporting expectations for entities operating in the EU. OBIS specifications in the regulatory-reporting focus area are intended to be compatible with these obligations and to reduce the integration cost of meeting them.

- **IVMS101** is the established data standard for originator and beneficiary identity information accompanying transfers between virtual asset service providers (VASPs). It is a transmittal standard, not an attribution standard. OBIS attribution work (e.g., [OBIS-0003]({{< relref "/standards/OBIS-0003" >}})) operates on a different segment of the workflow and does not subsume IVMS101.

- **INTERPOL DW-VA-Taxonomy** is a widely referenced taxonomy for darkweb and virtual-asset activity. Future OBIS classification work in this area will draw on it as prior art and aim to remain compatible where the underlying concepts overlap.

- **National supervisory frameworks** vary by jurisdiction. OBIS specifications are designed to be referenceable by supervisors that choose to adopt them, without prescribing adoption.

OBIS's contribution is the technical layer beneath these instruments, where the absence of shared formats and vocabularies currently imposes a cost on every party operating across organisational and jurisdictional boundaries.

## Process {#process}

An OBIS document progresses through three states: **Draft**, **Public Review**, and **Published**. Two further states, **Superseded** and **Withdrawn**, are terminal. Every document, in every state, lives on the main branch of the source repository, so its current text is always visible. Entering Public Review freezes the content for a comment window of 30 days by default. A document is Published only after that window has closed and the editor has responded publicly to every substantive comment, accepting it, declining it with a rationale, or deferring it.

The full process is specified in [OBIS-0001: OBIS Document Lifecycle]({{< relref "/standards/OBIS-0001" >}}), currently in Draft.

## Get involved {#get-involved}

Anyone interested in the work is welcome to follow it, comment on drafts, or contribute.

- **Drafts and discussion:** All specifications are developed in the [docs repository](https://github.com/obi-standards/docs), and each draft has a thread in [GitHub Discussions](https://github.com/orgs/obi-standards/discussions), the canonical venue for comments during Public Review. Anyone with a GitHub account can join a discussion, open issues, and submit pull requests. No application or membership is required. Ideas for new work items go to the [Proposals](https://github.com/orgs/obi-standards/discussions/categories/proposals) category, transitions to Public Review are announced under [Announcements](https://github.com/orgs/obi-standards/discussions/categories/announcements), and [How to participate in OBIS](https://github.com/orgs/obi-standards/discussions/5) gives the full channel map.

- **Email:** <info@obistandards.org>. General correspondence about the project.

- **Written commentary on drafts (institutional channel):** Institutions whose internal processes do not accommodate public GitHub workflows, such as supervisory bodies, financial intelligence units, and government agencies, may submit written commentary on any Draft or Public Review document by emailing <info@obistandards.org> with the subject line `Commentary: OBIS-NNNN`. The editor posts such commentary to the document's discussion thread, where it receives the same public response as any other comment. Senders may request that only the institutional affiliation be recorded, without naming the individual signatory.

### Why participate

Different kinds of organisations and individuals have different reasons to engage, and different forms of engagement suit them. The following framings are not membership categories. They describe how each audience typically relates to the work.

**Tool providers (blockchain analytics platforms, forensic tooling, on-chain data vendors).** OBIS specifications reduce the per-customer integration cost of supporting cross-organisation exchange. Vendors retain proprietary value in the data, attribution methods, and analytical capabilities they produce; OBIS standardises only the interchange formats and shared vocabularies between organisations. Participation lets a vendor influence the shape of those interfaces, surface implementation constraints early, and substantiate interoperability claims that procurement processes increasingly require. OBIS does not certify implementations, accredit vendors, or operate any process that ranks participating vendors against non-participating ones.

**Authorities (supervisors, financial intelligence units, prosecutors, central-bank analytics units, law-enforcement bodies).** OBIS specifications aim to make supervisory data more comparable across jurisdictions, reduce the bespoke-integration burden on reporting entities, and provide a shared technical baseline that prosecutors and courts can refer to when assessing the reliability of blockchain evidence. Authorities can engage as observers, as commenters on drafts in Public Review, or as co-editors and reviewers where their institution's mandate permits.

**Researchers (academic and applied).** OBIS specifications provide shared schemas, vocabularies, and reproducibility primitives that let work compound across studies rather than restart from raw chain history each time. Participation is valuable both as a contributor (proposing or co-editing specifications that codify what the field already knows) and as a user (citing OBIS specifications in methods sections, allowing other groups to reproduce and extend results). Research outputs that depend on specific attribution data, taxonomies, or data layers are particularly well-served by being expressed in terms of an open specification.

**Reporting entities (CASPs, exchanges, custodians, payment institutions handling virtual assets).** OBIS specifications in the regulatory-reporting focus area aim to reduce the cost of maintaining bespoke integrations with each supervisor and counterparty. Reporting entities have direct visibility into where bespoke-integration cost actually falls, which makes their input on draft exchange formats especially valuable. Engagement does not require a regulatory commitment of any kind; it is technical input into the design of formats that the entity may or may not eventually adopt.

### Co-editors and external reviewers

The current OBIS drafts are edited by a single founding editor. This reflects the project's early stage rather than its intended steady state. OBIS is actively seeking co-editors and named external reviewers for each draft, particularly from organisations and jurisdictions not yet represented in the contributor base.

Relevant expertise includes practitioner experience in investigations, supervisory or prosecutorial work, research on blockchain data, and implementation experience with blockchain analytics tooling. If you are interested in serving as a co-editor or named reviewer on one of the current drafts, open an issue in the [docs repository](https://github.com/obi-standards/docs/issues) or write to <info@obistandards.org>.