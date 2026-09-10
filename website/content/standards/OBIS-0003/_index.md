---
title: "OBIS-0003: Attribution Tag Data Model and Exchange Format"
type: docs
weight: 3
bookToc: true
status: "Draft"
date: "2026-05-19"
editor: "Bernhard Haslhofer"
focus_area: "Investigations and forensics"
discussions-to: "https://github.com/orgs/obi-standards/discussions/3"
---

# OBIS-0003: Attribution Tag Data Model and Exchange Format

{{< status state="Draft" date="2026-05-19" editor="Bernhard Haslhofer" focus="Investigations and forensics" >}}

## Abstract

Attribution, the association of a pseudonymous blockchain address with real-world context, is the core analytical activity in investigations, supervision, and research on blockchain data, but each platform represents the same underlying claim in its own format, so attributions produced in one ecosystem cannot be verified or reused in another. This document aims to make attribution claims portable across organisations and tools. It specifies a data model and exchange format for attribution tags: self-contained records that associate one blockchain address with a human-readable label, optionally refined by actor and abuse types, each carrying an attributor and human-readable evidence. The format is deliberately minimal; bundling, revocation, and machine-processable provenance are deferred to future revisions.

## 1. Introduction

Attribution is the core analytical activity in investigations, supervision, and research on blockchain data. Each platform working in this space today maintains its own representation of the same underlying claim. Identical concepts carry different names, and attributions produced in one ecosystem are difficult to verify or reuse in another.

OBIS-0003 specifies a minimal exchange format for attribution claims: which address, what real-world context, who asserts it, and on what evidence. A claim need not name an actor; a label may simply describe the role observed, refined where possible by actor and abuse types. The format is designed for exchange between organisations that maintain richer internal representations.

This document does not prescribe how attributions are produced. It specifies how they are represented when exchanged.

## 2. Scope

OBIS-0003 covers:

- the structure of an individual attribution claim (an "attribution tag") associated with a single blockchain address;
- the evidence metadata carried with a tag;
- the canonical wire serialization.

OBIS-0003 does not cover:

- the controlled vocabularies for actor and abuse types (not yet specified by OBIS; see §11);
- the production of attributions (heuristics, clustering, investigative method);
- transmittal protocols (push, pull, query); only the data format is normative.

## 3. Terminology

The key words **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).

- **Address.** As defined in [OBIS-0002]({{< relref "OBIS-0002" >}}) §4.3: an identifier that controls the receipt and spending of value on a blockchain, in the encoding defined by the respective chain's standards (e.g., the Bitcoin address formats specified in [BIP-173](https://github.com/bitcoin/bips/blob/master/bip-0173.mediawiki) and related BIPs, Ethereum account addresses with [EIP-55](https://eips.ethereum.org/EIPS/eip-55) checksumming).
- **Actor.** A real-world participant (e.g., service, organisation, natural person) to which an address may be attributed.
- **Attribution Tag (or "tag").** A single record associating one address with real-world context: a free-text label, optionally refined by actor types and abuse types. The label may name an actor (e.g., `binance`) or simply describe the address's role (e.g., `ransomware payment address`).
- **Attributor.** The organisation or individual asserting the tag.
- **Actor type / Abuse type.** Identifiers from a controlled vocabulary classifying, respectively, the kind of real-world participant an address represents and the kind of harmful activity observed. OBIS does not yet specify these vocabularies (§11); until it does, values are implementation-defined strings. A tag may carry several identifiers of either kind.

## 4. Design principles

OBIS-0003 commits to the following principles:

1. **Explicit chain identification.** Every tag names the chain its address lives on, alongside the address in the chain's native encoding. Nothing is inferred from address syntax; disambiguation across chains is a first-class concern.
2. **Controlled vocabularies for categorisation.** Actor and abuse classifications are identifiers from a controlled vocabulary, not free-form text. Until OBIS specifies such vocabularies (§11), producers SHOULD draw values from a documented vocabulary they name (e.g., the INTERPOL DW-VA-Taxonomy, §10.2). A tag MAY carry several identifiers of either kind.
3. **Human-readable evidence.** Every tag carries evidence that a human can assess: each evidence item has a description and optionally a URI pointing to a public source or a manifestation (e.g., a case file or screenshot). Machine-processable provenance chains are deferred (§11).
4. **Address-scoped tags.** A tag provides context for exactly one address. Any extension of an attribution beyond the named address (e.g., via clustering) is the consumer's responsibility and is out of scope.

## 5. Identifiers

A tag identifies its subject with two fields: `chain`, a lowercase chain identifier (e.g., `bitcoin`, `ethereum`, `tron`), and `address`, the address string in the chain's native encoding. Implementations MUST NOT infer the chain from address syntax.

A shared registry of chain identifiers, including testnet and fork disambiguation, is deferred (§11). Until it exists, attributors SHOULD use the lowercased form of the chain names established by community-curated lists (e.g., the [DefiLlama chain list](https://defillama.com/chains)).

## 6. Data model

An attribution tag is a single record with the following fields:

| Field | Type | Required | Description |
|---|---|---|---|
| `chain` | string | yes | Lowercase chain identifier (§5). |
| `address` | string | yes | Address in the chain's native encoding (§5). |
| `label` | string | yes | Human-readable, free-text label providing context for the address; may name an actor (e.g., `binance`) or describe a role (e.g., `ransomware payment address`). |
| `actor_types` | array of strings | optional | Actor-type identifiers from a controlled vocabulary; vocabulary implementation-defined pending an OBIS classification document (§11). |
| `abuse_types` | array of strings | optional | Abuse-type identifiers from a controlled vocabulary; vocabulary implementation-defined pending an OBIS classification document (§11). |
| `attributor` | string | yes | URI or name identifying the organisation or individual asserting the tag. |
| `evidence` | array | yes | Evidence items supporting the claim; at least one. |

The label is mandatory: it is the context a human reader sees first and is typically what implementations display alongside the address. `actor_types` and `abuse_types` refine it with machine-comparable concepts.

Each evidence item is an object with a required `description`, a human-readable account of what supports the claim, and an optional `uri` pointing either to a public source or to a manifestation of the evidence (e.g., a case file or screenshot).

A tag applies to exactly the address named in its `address` field. Any propagation to other addresses is the consumer's responsibility and is out of scope.

## 7. Serialization

The canonical exchange serialization is **JSON**. The exchange unit is a JSON array of tag objects; a single tag is exchanged as an array of one. Each tag is self-contained; there is no shared header and no cross-tag inheritance.

Example:

```json
[
  {
    "chain": "bitcoin",
    "address": "34xp4vRoCGJym3xR7yCVPFHoCNxv4Twseo",
    "label": "Binance cold wallet",
    "attributor": "https://attributor.example/",
    "evidence": [
      {
        "description": "Listed in Binance's proof-of-reserves disclosure",
        "uri": "https://www.binance.com/en/proof-of-reserves"
      }
    ]
  },
  {
    "chain": "bitcoin",
    "address": "1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa",
    "label": "mixer-x",
    "actor_types": ["tumbler"],
    "attributor": "https://attributor.example/",
    "evidence": [
      {
        "description": "OFAC SDN designation of mixer-x, 2024-08-01",
        "uri": "https://home.treasury.gov/news/press-releases/example"
      }
    ]
  },
  {
    "chain": "ethereum",
    "address": "0xAb16a96d359eC26A11e2c2B3D8F8b8942d5bfCdB",
    "label": "ransom payment address",
    "abuse_types": ["ransomware"],
    "attributor": "Attributor XY",
    "evidence": [
      {
        "description": "Ransom payment address from case 2025/0417; ransom note screenshot on record with the attributor"
      }
    ]
  }
]
```

The first tag is minimal: a label and one evidence item. The third tag's label describes the address's role without naming an actor, and its attributor is identified by name rather than URI.

## 8. Privacy and security considerations

Attribution tags are claims about real-world actors. Where the actor is a natural person, a tag constitutes personal data, and exchanging it can violate data protection regulation (e.g., the GDPR) if no lawful basis for the transfer exists.

1. Implementations MUST treat attributions that identify a natural person as personal data and restrict their storage, exchange, and onward disclosure to recipients with a lawful basis.
2. Evidence URIs MUST NOT point to resources disclosing personal data beyond what the attribution itself reveals (e.g., a link to a leaked document containing additional personal data is not appropriate evidence).
3. Tags whose abuse types indicate CSAM or terrorism financing SHOULD be exchanged only with appropriately accredited recipients.

## 9. Conformance

An implementation is conformant with this document if, when exchanging attribution data with another party:

1. it produces records using the field names, types, and constraints defined in §6, including the mandatory `label`;
2. it identifies addresses with explicit `chain` and `address` fields (§5);
3. it populates `actor_types` and `abuse_types`, where present, with identifiers from a documented controlled vocabulary (§4);
4. it carries at least one evidence item on every tag; and
5. it applies the privacy provisions of §8.

Conformance does not require an implementation to produce tags. A read-only consumer is conformant if it preserves the above when re-emitting received records.

## 10. Related work

### 10.1 GraphSense TagPacks

GraphSense [TagPacks](https://github.com/graphsense/graphsense-tagpacks/wiki/GraphSense-TagPacks) are the most widely used open format for sharing attribution data in the investigations community. A TagPack is a YAML file containing a header (`title`, `creator`, default `currency`, default `source`, etc.) and a list of tag records (`address`, `label`, `source`, optional `category`, `abuse`, `confidence`), with header fields cascading as defaults to contained tags. OBIS-0003 adopts the tag idea and simplifies the rest: bundles and header inheritance are deferred (§11), the chain is named explicitly rather than by currency shorthand, and an evidence list replaces the single `source` field. It does not adopt the TagPack `confidence` field; assessing the credibility of a tag is the consumer's responsibility, informed by the evidence carried with the tag.

### 10.2 INTERPOL DW-VA-Taxonomy

The category and abuse fields in GraphSense TagPacks draw from the [INTERPOL DW-VA-Taxonomy](https://interpol-innovation-centre.github.io/DW-VA-Taxonomy/). An OBIS classification scheme for actor and abuse types is deferred ([OBIS-0002]({{< relref "OBIS-0002" >}}) §9); the INTERPOL taxonomy is the expected base for that work and, in the interim, a documented vocabulary producers may name for `actor_types` and `abuse_types`.

### 10.3 W3C PROV Data Model

The [W3C PROV Data Model](https://www.w3.org/TR/prov-dm/) is the established framework for representing provenance. The evidence model in this document is deliberately simpler: a human-readable description with an optional URI. A future revision may align evidence with PROV vocabulary and define a normative PROV-O mapping (§11).

### 10.4 FATF Travel Rule and IVMS101

The [FATF Recommendation 16](https://www.fatf-gafi.org/) (the "Travel Rule") and the associated [IVMS101](https://www.intervasp.org/) data standard specify how originator and beneficiary identity information accompanies VASP-to-VASP transfers. IVMS101 is a transmittal standard, not an attribution standard: it carries identity disclosed by counterparties at transfer time, rather than third-party-asserted bindings of addresses to actors. The two formats are complementary and operate on disjoint segments of the regulatory workflow. OBIS-0003 does not subsume IVMS101 and does not depend on it.

### 10.5 Closed commercial formats

The major commercial blockchain analytics vendors (Chainalysis, TRM Labs, Elliptic) each maintain proprietary attribution-tagging formats. These are not publicly redistributable and vary in field structure. They are noted because operational interoperability with these vendors requires bidirectional mapping. They cannot serve as the basis for an open standard.

## 11. Open issues

- **Actor and abuse vocabularies.** The controlled vocabularies for `actor_types` and `abuse_types` are deferred to a future OBIS classification document ([OBIS-0002]({{< relref "OBIS-0002" >}}) §9); until then both fields are unconstrained beyond §4 and the vocabulary in use SHOULD be documented by the producer.
- **Tag bundles.** Bulk exchange with shared header metadata and defaults inheritance is deferred; until then, the exchange unit is an array of self-contained tags (§7).
- **Custom fields.** An extension mechanism allowing tool providers to attach custom fields to a tag (e.g., under a reserved prefix or a dedicated extensions object) without breaking interoperability is deferred; until then, receivers SHOULD ignore unknown fields.
- **Revocation and versioning.** Correcting or withdrawing exchanged tags (revocation records, supersession of earlier releases) is deferred to a future revision.
- **Chain identifier registry.** A shared registry of chain identifiers, including testnets and forks, is deferred; community-curated lists serve in the interim (§5).
- **Structured provenance.** Machine-processable lineage (derivation chains between tags, a normative PROV-O / JSON-LD `@context` mapping) is deferred.
- **Signing.** Cryptographic signing of exchanged tags for authenticity and non-repudiation is deferred.

## References

- IETF [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119), *Key words for use in RFCs to Indicate Requirement Levels*.
- Bitcoin, [BIP-173](https://github.com/bitcoin/bips/blob/master/bip-0173.mediawiki), *Base32 address format for native v0-16 witness outputs*.
- Ethereum, [EIP-55](https://eips.ethereum.org/EIPS/eip-55), *Mixed-case checksum address encoding*.
- W3C, [*PROV Data Model*](https://www.w3.org/TR/prov-dm/).
- GraphSense, [*TagPacks Wiki*](https://github.com/graphsense/graphsense-tagpacks/wiki/GraphSense-TagPacks).
- INTERPOL Innovation Centre, [*Darkweb and Virtual Assets Taxonomy*](https://interpol-innovation-centre.github.io/DW-VA-Taxonomy/).
- FATF, [*Recommendation 16 (Travel Rule)*](https://www.fatf-gafi.org/).
- InterVASP, [*IVMS101 Data Standard*](https://www.intervasp.org/).
- DefiLlama, [*Chains*](https://defillama.com/chains).
- [OBIS-0001]({{< relref "OBIS-0001" >}}), *OBIS Document Lifecycle*.
- [OBIS-0002]({{< relref "OBIS-0002" >}}), *Shared Vocabulary for Blockchain Intelligence*.
