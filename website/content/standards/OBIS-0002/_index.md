---
title: "OBIS-0002: Shared Vocabulary for Blockchain Intelligence"
type: docs
weight: 2
bookToc: true
status: "Draft"
date: "2026-09-10"
editor: "Bernhard Haslhofer"
focus_area: "Terminology"
discussions-to: "https://github.com/orgs/obi-standards/discussions/2"
---

# OBIS-0002: Shared Vocabulary for Blockchain Intelligence

{{< status state="Draft" date="2026-09-10" editor="Bernhard Haslhofer" focus="Terminology" >}}

## Abstract

Blockchain intelligence work is described in a small set of recurring terms: block, transaction, address, wallet, custodial, hot and cold storage, clustering, cluster. Each is used with different meanings by analytics vendors, investigators, regulators, and researchers, so that statements made in one context cannot be reliably interpreted in another. This document defines a shared vocabulary of such terms. Each term carries a stable identifier and a normative definition, together with informative notes on chain-specific variation and common misuse, so that OBIS specifications and independent implementations can refer to the same concept by the same name. The vocabulary is organised in four groups: technical primitives, custody and control, wallet operational roles, and computational methods.

## 1. Introduction

Many disagreements in the exchange of blockchain intelligence are not disagreements about facts on the chain but about words. "Wallet" denotes a piece of key-management software in one report, a set of addresses inferred by a heuristic in another, and a real-world account holder in a third. "Cold wallet" is variously a device, a role, or a balance tier. "Cluster" is sometimes a hypothesis about common control and sometimes an entity with a name attached. Statements that reuse these words without fixing their meaning inherit the ambiguity, and classification schemes built on top of them cannot be more precise than the terms they rest on.

OBIS-0002 therefore starts at the level of definitions. It fixes the meaning of a small number of terms that recur across OBIS specifications and across the wider practice of blockchain intelligence. Where existing definitions are precise enough, the vocabulary adopts them and says where they come from. Where usage is inconsistent, the vocabulary makes a choice and records the alternatives it departs from.

The vocabulary is chain-agnostic. Where a term means something different under the unspent-transaction-output (UTXO) model and the account model, the entry says so rather than privileging one.

## 2. Scope

This document defines terms in four groups:

- **Technical primitives** (§4): the on-chain objects intelligence work operates on.
- **Custody and control** (§5): who holds the keys, and on whose behalf.
- **Wallet operational roles** (§6): how operators expose signing keys in practice.
- **Computational methods** (§7): how addresses are grouped by inference.

Out of scope:

- classification of actors and abuse types; earlier revisions of this document specified such schemes, and the work is deferred to a separate document (§9);
- legal definitions, such as virtual asset service provider (VASP) or crypto-asset service provider (CASP), which are referenced where relevant and not redefined;
- protocol detail beyond what is needed to interpret intelligence data.

## 3. Conventions

The key words **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).

Each term is presented as an entry with the following parts:

- **Identifier.** A stable, lower-case, hyphen-separated token (e.g., `hot-wallet`) by which OBIS documents and implementations refer to the term. Identifiers are reserved by OBIS and do not change once assigned; the preferred label and the definition may be revised.
- **Definition.** The normative meaning of the term, in English.
- **Notes.** Informative remarks on chain-specific variation, boundary cases, and usages this vocabulary departs from.
- **Sources.** Informative pointers to prior definitions the entry aligns with or diverges from.

Documents and implementations that reference this vocabulary SHOULD use the identifier, and MAY additionally display the label in any language. A term used in an OBIS document with the meaning defined here is linked to its entry on first use.

## 4. Technical primitives

### 4.1 Block

**Identifier:** `block`

**Definition.** A set of transactions, ordered and committed to a blockchain as a single unit, that references its predecessor by cryptographic hash.

**Notes.**

- The attributes of a block that intelligence work relies on are its *height* (position in the chain) and its *timestamp*, which together order and date the transactions it contains. Timestamps are set by the block producer and are approximate.
- A block may contain no transactions.
- Whether a block is final differs by chain: in proof-of-work systems finality is probabilistic and grows with the number of succeeding blocks; in proof-of-stake systems it is typically established by a separate finalisation step. Intelligence data derived from recent blocks may be subject to reorganisation.

**Sources.** Nakamoto (2008) §4; Wood, *Ethereum Yellow Paper*, §4.3.

### 4.2 Transaction

**Identifier:** `transaction`

**Definition.** A signed message, recorded in a block, that transfers value or invokes code on a blockchain, and that is identified by its transaction hash.

**Notes.**

- Under the UTXO model a transaction consumes one or more previously created outputs as *inputs* and creates new *outputs*; there is no single sender or recipient, and value flow is read from the set of inputs and outputs.
- Under the account model a transaction has exactly one sender account and one recipient account or contract, a value, and optional call data.
- Value movements that occur as a *consequence* of a transaction, such as transfers triggered by contract code (EVM "internal transactions" or traces) or token transfers recorded in event logs, are effects of a transaction and are not transactions in the sense of this entry. Intelligence data SHOULD state whether it accounts for such effects.
- A transaction that has been broadcast but not yet included in a block is *unconfirmed*; the term as defined here refers to recorded transactions unless stated otherwise.

**Sources.** Nakamoto (2008) §2; Wood, *Ethereum Yellow Paper*, §4.2.

### 4.3 Address

**Identifier:** `address`

**Definition.** A chain-specific identifier, derived from a public key, a script, or a contract creation, that designates where value can be received on a blockchain and the condition under which it can be spent.

**Notes.**

- On Bitcoin an address is the encoding of a spending condition: a hash of a public key or of a script, in the Base58Check formats of [BIP-13](https://github.com/bitcoin/bips/blob/master/bip-0013.mediawiki) or the Bech32 and Bech32m formats of [BIP-173](https://github.com/bitcoin/bips/blob/master/bip-0173.mediawiki) and [BIP-350](https://github.com/bitcoin/bips/blob/master/bip-0350.mediawiki). Addresses do not appear in the chain as such; transactions carry the underlying scripts.
- On Ethereum an address is the rightmost 160 bits of the Keccak-256 hash of an account's public key, or, for a contract, of the creator's address and nonce (Yellow Paper §7); the mixed-case checksum encoding is specified in [EIP-55](https://eips.ethereum.org/EIPS/eip-55).
- An address is not an actor. The same party typically controls many addresses, and hierarchical-deterministic wallets generate new addresses on demand; conversely a contract address is controlled by code rather than by a key.
- Addresses are scoped to a chain. The same string may be valid on several chains, as with the shared address format of EVM-compatible chains, and denote unrelated control; intelligence data therefore identifies the chain together with the address.

**Sources.** Bitcoin Project, *Developer Glossary*, "Address"; Ethereum Foundation, *Ethereum Glossary*, "Address"; Wood, *Ethereum Yellow Paper*, §7; BIP-13, BIP-173, BIP-350; EIP-55.

## 5. Custody and control

### 5.1 Wallet

**Identifier:** `wallet`

**Definition.** Software, hardware, or a service that manages the private keys controlling one or more addresses, and through those keys the ability to transfer value from those addresses.

**Notes.**

- A wallet is an off-chain instrument of control. It has no on-chain representation; what is observable on chain are the addresses it controls and the transactions it signs.
- One wallet controls many addresses. The relation between a wallet and the addresses it controls is generally not observable and must be inferred (§7).
- In vendor and investigative usage "wallet" is frequently used for a set of addresses inferred to be under common control. This vocabulary reserves `cluster` (§7.3) for that meaning: a cluster is an inference about a wallet, not the wallet itself.
- Smart-contract wallets (multi-signature contracts, account-abstraction accounts) hold value in a contract whose transfer rules are code. They are wallets in the sense of this entry, with the parties authorised by the contract as key holders.

**Sources.** Chainalysis, *Crypto Glossary*, "Crypto Wallet" (aligned: a tool that stores the keys used to access and manage crypto-assets).

### 5.2 Custodial wallet

**Identifier:** `custodial-wallet`

**Definition.** A wallet whose private keys are held by a party other than the holder of the value it controls, so that a transfer requires the action of that party.

**Notes.**

- The key-holding party is the *custodian*; the holder has a claim against the custodian rather than on-chain control. Custodians are typically services subject to VASP or CASP obligations.
- On chain, custodial wallets appear as addresses operated by the custodian. Value belonging to many holders is commonly pooled in the custodian's addresses, so that an address of a custodial wallet does not correspond to one holder.
- The test is control over the keys, not the legal form of the relationship. This follows the FATF approach, under which a service that holds or controls private keys on behalf of others provides custody.

**Sources.** FATF (2021), *Updated Guidance for a Risk-Based Approach to Virtual Assets and Virtual Asset Service Providers*; Regulation (EU) 2023/1114 (MiCA), Art. 3(1)(17) ("providing custody and administration of crypto-assets on behalf of clients"); Chainalysis, *Crypto Glossary*, "Crypto Wallet".

### 5.3 Non-custodial wallet

**Identifier:** `non-custodial-wallet`

**Definition.** A wallet whose private keys are held by the holder of the value it controls, so that no other party can transfer that value.

**Notes.**

- Also called *self-custody*, *self-hosted*, or, in regulatory texts, *unhosted* wallet. Hardware wallets, mobile and browser wallets, and paper keys are non-custodial when the holder alone keeps the keys.
- Multi-signature arrangements in which all key holders are the same party are non-custodial.
- Arrangements that split keys between the holder and a service (e.g., a 2-of-3 scheme with a recovery key held by a provider) are classified by who can complete a transfer without the other's participation. Where neither party can, the arrangement is a shared-custody boundary case; its treatment is deferred (§9).

**Sources.** FATF (2021), which uses "unhosted wallet" for this case; Chainalysis, *Crypto Glossary*, "Crypto Wallet".

## 6. Wallet operational roles

Operators of services distinguish their wallets by how signing keys are exposed: continuously online and signing automatically, online but signing under approval, or offline. These are *roles* assigned by the operator's practice, not properties of a technology, and they are typically inferred from transaction patterns rather than observed on chain. Intelligence work uses them to interpret flows, for instance sweeps from deposit addresses into a hot wallet, or periodic transfers between hot and cold storage.

### 6.1 Hot wallet

**Identifier:** `hot-wallet`

**Definition.** A wallet whose signing keys are held on a system connected to a network and that signs transactions automatically, without per-transaction human approval.

**Notes.**

- Hot wallets serve continuous operations such as processing customer withdrawals and consolidating deposits. They carry the highest exposure to key compromise and typically hold a small share of an operator's total balance.
- Connectivity alone does not make a wallet hot; the defining property is automatic signing. A wallet that is online but signs only under approval is a warm wallet (§6.2).

**Sources.** Chainalysis, *Crypto Glossary*, "Crypto Wallet" (aligned on connectivity; this entry adds the signing-policy criterion).

### 6.2 Warm wallet

**Identifier:** `warm-wallet`

**Definition.** A wallet whose signing keys are held on a system that can connect to a network, but that signs only after manual or policy-based approval of each transaction.

**Notes.**

- Warm wallets form an intermediate liquidity tier: they replenish hot wallets and receive value from cold storage, under controls such as multi-party approval, transaction limits, and address allow-lists.
- No widely shared definition exists; vendor glossaries commonly define hot and cold wallets only. This vocabulary distinguishes warm from hot by signing policy and warm from cold by whether the signing system can connect to a network.

**Sources.** CryptoCurrency Certification Consortium (C4), *CryptoCurrency Security Standard*, which specifies the key-management controls operators apply to such wallets without naming the roles.

### 6.3 Cold wallet

**Identifier:** `cold-wallet`

**Definition.** A wallet whose signing keys are generated and held on systems that are never connected to a network, so that transactions are signed offline and transferred to a connected system for broadcast.

**Notes.**

- Cold storage holds the bulk of an operator's balance; movements from it are infrequent and typically large.
- The role is defined by practice, not by device. A hardware wallet used for daily signing on a connected host is not a cold wallet in the sense of this entry.

**Sources.** Chainalysis, *Crypto Glossary*, "Crypto Wallet" (aligned: keys kept offline).

## 7. Computational methods

### 7.1 Address clustering

**Identifier:** `address-clustering`

**Definition.** The process of grouping addresses that are presumed to be controlled by the same party, based on evidence observable on the blockchain and on one or more stated clustering heuristics.

**Notes.**

- Clustering is inference, not observation. Its result is only interpretable together with the heuristics that produced it; intelligence data derived from clustering SHOULD state those heuristics.
- Under the UTXO model the canonical heuristic is the *multi-input* (or *co-spend*) heuristic: all inputs of a transaction are presumed to be controlled by the same party. It fails for collaborative transactions such as CoinJoin, which are constructed to violate it. The *change-address* heuristic identifies which output of a transaction returns value to the sender and links it to the inputs.
- Under the account model there is no co-spending; clustering rests on other signals, such as the sweeping of deposit addresses into a service's hot wallet, or funding relations between addresses.

**Sources.** Meiklejohn et al. (2013); Androulaki et al. (2013); Harrigan and Fretter (2016); Chainalysis, *Crypto Glossary*, "Address Clustering" (aligned on the definition).

### 7.2 Clustering heuristic

**Identifier:** `clustering-heuristic`

**Definition.** A rule that infers common control of two or more addresses from a pattern in on-chain data, together with the conditions under which the inference is known to fail.

**Notes.**

- A heuristic without a statement of its failure conditions is not sufficiently specified for exchange. Reference specifications of individual heuristics are planned as separate OBIS work (§9).

**Sources.** Meiklejohn et al. (2013) §4; Harrigan and Fretter (2016).

### 7.3 Cluster

**Identifier:** `cluster`

**Definition.** A set of addresses grouped by address clustering under stated heuristics. A cluster expresses a hypothesis of common control and carries no attribution to a real-world actor by itself.

**Notes.**

- A cluster is not a wallet. One wallet may span several clusters where the heuristics miss a link, and one cluster may merge several wallets where a heuristic fails.
- A cluster is not an actor. A cluster becomes attributed only when evidence links it, or an address in it, to a real-world actor. Whether an attribution established for one address extends to the cluster containing it is a judgement of the consumer, which depends on the heuristics behind the cluster.
- Vendor usage often calls an attributed cluster an *entity*. This vocabulary does not yet define *entity*; see §9.

**Sources.** Chainalysis, *Crypto Glossary*, "Address Clustering", which defines a cluster as attributed to a named actor; this entry departs by separating the inference (cluster) from the attribution.

## 8. Related work

### 8.1 Vendor glossaries

Chainalysis publishes a public [Crypto Glossary](https://www.chainalysis.com/glossary/) covering, among other terms, [address clustering](https://www.chainalysis.com/glossary/address-clustering/) and [crypto wallets](https://www.chainalysis.com/glossary/crypto-wallet/). It is the most visible vendor vocabulary in the field and the reference point for much investigative usage. This document aligns with it on the definition of address clustering and on the custodial and hot/cold distinctions, and departs from it in two places: it defines a warm-wallet role, and it treats a cluster as a hypothesis of common control rather than as an attributed entity. Other vendors (e.g., TRM Labs, Elliptic) maintain their own terminology in product documentation that is not consistently public.

### 8.2 Protocol developer glossaries

The [Bitcoin developer glossary](https://developer.bitcoin.org/glossary.html) and the [ethereum.org glossary](https://ethereum.org/en/glossary/) define the technical primitives precisely for their respective chains. They are the sources for §4 but are silent on custody, operational roles, and clustering, which are concerns of intelligence work rather than of protocol design.

### 8.3 Regulatory definitions

The FATF *Updated Guidance for a Risk-Based Approach to Virtual Assets and Virtual Asset Service Providers* (2021) and Regulation (EU) 2023/1114 (MiCA) define custody in terms of holding or controlling private keys on behalf of others, and use "unhosted" or "self-hosted" wallet for the non-custodial case. §5 follows these definitions and references them rather than restating them; the legal categories VASP and CASP themselves are out of scope.

### 8.4 INTERPOL DW-VA-Taxonomy and GraphSense conventions

The [INTERPOL Darkweb and Virtual Assets Taxonomy](https://interpol-innovation-centre.github.io/DW-VA-Taxonomy/) (Entity Taxonomy v0.3, Abuse Taxonomy v0.1) classifies actors and abuses in the dark-web and cryptoasset ecosystems, and GraphSense [TagPacks](https://github.com/graphsense/graphsense-tagpacks/wiki/GraphSense-TagPacks) reuse it for their `category` and `abuse` fields. Both are classification schemes rather than vocabularies. They are the expected starting point for the actor and abuse classification work that earlier revisions of this document contained and that is now deferred (§9).

## 9. Open issues

- **Actor and abuse classification.** Earlier revisions of this document specified Actor Type and Abuse Type concept schemes with an extension mechanism. That material is withdrawn from this document and will be reintroduced as a separate OBIS classification document built on this vocabulary. Until then OBIS specifies no actor or abuse categories.
- **Further terms.** Candidates for later revisions include *actor*, *entity*, *service*, *attribution*, *label*, *transaction graph*, *mixer* and *CoinJoin*, and *bridge*. Proposals go to the discussion thread linked in the status block.
- **Shared custody.** Arrangements in which neither the holder nor a service can complete a transfer alone (§5.3) are not yet classified.
- **Heuristic specifications.** Reference specifications of individual clustering heuristics, including their failure conditions, are planned as separate OBIS work.
- **Serialisation.** A machine-readable representation of the vocabulary (e.g., SKOS/RDF or JSON) is deferred; the entries in this document are the normative representation.
- **Multilingual labels.** Labels in languages other than English are deferred.

## References

- IETF [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119), *Key words for use in RFCs to Indicate Requirement Levels*.
- S. Nakamoto, [*Bitcoin: A Peer-to-Peer Electronic Cash System*](https://bitcoin.org/bitcoin.pdf), 2008.
- G. Wood, [*Ethereum: A Secure Decentralised Generalised Transaction Ledger*](https://ethereum.github.io/yellowpaper/paper.pdf) (Yellow Paper).
- Bitcoin, [BIP-13](https://github.com/bitcoin/bips/blob/master/bip-0013.mediawiki), *Address Format for pay-to-script-hash*.
- Bitcoin, [BIP-173](https://github.com/bitcoin/bips/blob/master/bip-0173.mediawiki), *Base32 address format for native v0-16 witness outputs*.
- Bitcoin, [BIP-350](https://github.com/bitcoin/bips/blob/master/bip-0350.mediawiki), *Bech32m format for v1+ witness addresses*.
- Ethereum, [EIP-55](https://eips.ethereum.org/EIPS/eip-55), *Mixed-case checksum address encoding*.
- Bitcoin Project, [*Developer Glossary*](https://developer.bitcoin.org/glossary.html).
- Ethereum Foundation, [*Ethereum Glossary*](https://ethereum.org/en/glossary/).
- FATF, [*Updated Guidance for a Risk-Based Approach to Virtual Assets and Virtual Asset Service Providers*](https://www.fatf-gafi.org/en/publications/Fatfrecommendations/Guidance-rba-virtual-assets-2021.html), October 2021.
- European Union, [Regulation (EU) 2023/1114 on markets in crypto-assets (MiCA)](https://eur-lex.europa.eu/eli/reg/2023/1114/oj).
- CryptoCurrency Certification Consortium (C4), [*CryptoCurrency Security Standard (CCSS)*](https://cryptoconsortium.org/ccss/).
- Chainalysis, [*Crypto Glossary*](https://www.chainalysis.com/glossary/).
- S. Meiklejohn et al., [*A Fistful of Bitcoins: Characterizing Payments Among Men with No Names*](https://doi.org/10.1145/2504730.2504747), Proc. IMC 2013.
- E. Androulaki et al., [*Evaluating User Privacy in Bitcoin*](https://doi.org/10.1007/978-3-642-39884-1_4), Proc. Financial Cryptography and Data Security 2013.
- M. Harrigan and C. Fretter, [*The Unreasonable Effectiveness of Address Clustering*](https://arxiv.org/abs/1605.06369), 2016.
- INTERPOL Innovation Centre, [*Darkweb and Virtual Assets Taxonomy*](https://interpol-innovation-centre.github.io/DW-VA-Taxonomy/). Entity Taxonomy v0.3, Abuse Taxonomy v0.1.
- GraphSense, [*TagPacks Wiki*](https://github.com/graphsense/graphsense-tagpacks/wiki/GraphSense-TagPacks).
- [OBIS-0001]({{< relref "OBIS-0001" >}}), *OBIS Document Lifecycle*.
