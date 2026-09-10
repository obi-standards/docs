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

Blockchain intelligence relies on a small set of recurring terms (e.g., address, wallet, cluster) that analytics vendors, investigators, regulators, and researchers use with different meanings, so that a statement made in one context cannot be reliably interpreted in another. This document defines a shared vocabulary for such terms. Each entry consists of a stable identifier, a normative definition, and informative notes on chain-specific variation and divergent usage, so that OBIS specifications and independent implementations can refer to the same concept by the same name. The vocabulary is chain-agnostic and organised in four groups: technical primitives, custody and control, wallet operational roles, and computational methods.

## 1. Introduction

Many disagreements in the exchange of blockchain intelligence are not disagreements about facts on the chain but about words. "Wallet" denotes a piece of key-management software in one report and a set of addresses inferred by a heuristic in another. "Cold wallet" is variously a device, a role, or a balance tier. "Cluster" is sometimes a hypothesis about common control and sometimes an entity with a name attached. Statements that reuse these words without fixing their meaning inherit the ambiguity, and any classification of actors or abuses built on top of them cannot be more precise than the terms it rests on.

OBIS-0002 therefore confines itself to definitions. It fixes the meaning of a small number of terms that recur across OBIS specifications and across the wider practice of blockchain intelligence. Where an existing definition is precise enough for the exchange of intelligence data, the vocabulary adopts it and states its source. Where usage is inconsistent, the vocabulary makes a choice and records the alternatives it departs from. Where no shared definition exists, it proposes one and marks it as such.

The vocabulary is chain-agnostic. Where a term means something different under the unspent-transaction-output (UTXO) model and the account model, the entry says so rather than adopting the meaning of one model.

## 2. Scope

This document defines terms in four groups:

- **Technical primitives** (§4): the on-chain objects intelligence work operates on.
- **Custody and control** (§5): who holds the keys, and on whose behalf.
- **Wallet operational roles** (§6): how operators expose signing keys in practice.
- **Computational methods** (§7): how addresses are grouped by inference.

Out of scope:

- classification of actors and abuse types, which earlier revisions of this document contained and which is deferred to a separate document (§9);
- legal definitions, such as virtual asset service provider (VASP) or crypto-asset service provider (CASP), which are referenced where relevant and not redefined;
- protocol detail beyond what is needed to interpret intelligence data.

## 3. Conventions

The key words **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** in this document are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119) and [RFC 8174](https://www.rfc-editor.org/rfc/rfc8174) when, and only when, they appear in all capitals.

Each term is presented as an entry with the following parts:

- **Label.** The English name of the term, used as the entry heading.
- **Identifier.** A stable, lower-case, hyphen-separated token (e.g., `hot-wallet`) by which OBIS documents and implementations refer to the term. Identifiers are reserved by OBIS and do not change once assigned; the label and the definition may be revised.
- **Definition.** The normative meaning of the term.
- **Notes.** Informative remarks on chain-specific variation, boundary cases, and usages this vocabulary departs from.
- **Sources.** Informative pointers to prior definitions the entry aligns with or diverges from.

Documents and implementations that reference this vocabulary SHOULD use the identifier, and MAY additionally display the label, in English or in another language. An OBIS document that uses a term with the meaning defined here SHOULD link to its entry on first use.

## 4. Technical primitives

### 4.1 Block

**Identifier.** `block`

**Definition.** A set of transactions, ordered and committed to a blockchain as a single unit, that references its predecessor by cryptographic hash.

**Notes.**

- The attributes of a block that intelligence work relies on are its *height* (position in the chain) and its *timestamp*, which together order and date the transactions it contains. Timestamps are set by the block producer and are approximate.
- A block may contain no transactions.
- Whether a block is final differs by chain: in proof-of-work systems finality is probabilistic and grows with the number of succeeding blocks; in proof-of-stake systems it is typically established by a separate finalisation step. Intelligence data derived from recent blocks may be subject to reorganisation.

**Sources.** Nakamoto (2008) §3–4; Wood, *Ethereum Yellow Paper*, §4.4.

### 4.2 Transaction

**Identifier.** `transaction`

**Definition.** A message, recorded in a block and identified by its transaction hash, that transfers value, creates a contract, or invokes code on a blockchain.

**Notes.**

- Transactions are authorised by the signatures of the parties that control the value or accounts they spend from. The exception is the coinbase transaction of a UTXO-model block, which issues new value and consumes no prior outputs, and is authored by the block producer without a signature.
- Under the UTXO model a transaction consumes one or more previously created outputs as *inputs* and creates new *outputs*; there is no single sender or recipient, and value flow is read from the set of inputs and outputs.
- Under the account model a transaction has exactly one sender account, a value, optional call data, and either one recipient account or contract or, for contract creation, no recipient.
- Value movements that occur as a *consequence* of a transaction, such as transfers triggered by contract code (EVM "internal transactions" or traces) or token transfers recorded in event logs, are effects of a transaction and are not transactions in the sense of this entry. Intelligence data should state whether it accounts for such effects.
- A transaction that has been broadcast but not yet included in a block is *unconfirmed*; the term as defined here refers to recorded transactions unless stated otherwise.

**Sources.** Nakamoto (2008) §2; Wood, *Ethereum Yellow Paper*, §4.2.

### 4.3 Address

**Identifier.** `address`

**Definition.** A chain-specific identifier, derived from a public key, a script, or a contract creation, that designates an account or an output script to which value can be sent on a blockchain.

**Notes.**

- On Bitcoin an address is the encoding of a spending condition: the hash of a public key or of a script, or, for Taproot outputs ([BIP-341](https://github.com/bitcoin/bips/blob/master/bip-0341.mediawiki)), a tweaked public key. Legacy addresses use Base58Check (for pay-to-script-hash per [BIP-13](https://github.com/bitcoin/bips/blob/master/bip-0013.mediawiki)); native SegWit addresses use the Bech32 and Bech32m formats of [BIP-173](https://github.com/bitcoin/bips/blob/master/bip-0173.mediawiki) and [BIP-350](https://github.com/bitcoin/bips/blob/master/bip-0350.mediawiki). Addresses do not appear in the chain as such; transactions carry the underlying scripts.
- On Ethereum an address is the rightmost 160 bits of the Keccak-256 hash of an account's public key or, for a contract, of the creator's address and nonce (Yellow Paper §7) or of a salt and the contract's initialisation code ([EIP-1014](https://eips.ethereum.org/EIPS/eip-1014)), which makes some contract addresses predictable before deployment; the mixed-case checksum encoding is specified in [EIP-55](https://eips.ethereum.org/EIPS/eip-55).
- An address is not an actor. The same party typically controls many addresses, and hierarchical-deterministic wallets generate new addresses on demand. A contract address is controlled by code rather than by a key.
- Addresses are scoped to a chain. The same address string is valid on all chains that use the Ethereum address format. For an externally owned account it is controlled by the same key on each of them, but balance and history differ per chain, and a contract deployed at an address on one chain may be absent or different on another. Intelligence data therefore identifies the chain together with the address.

**Sources.** Bitcoin Project, *Developer Glossary*, "Address"; Ethereum Foundation, *Ethereum Glossary*, "Address"; Wood, *Ethereum Yellow Paper*, §7; BIP-13, BIP-173, BIP-341, BIP-350; EIP-55, EIP-1014.

## 5. Custody and control

### 5.1 Wallet

**Identifier.** `wallet`

**Definition.** Software, hardware, or a service that holds the private keys controlling one or more addresses and signs transactions from those addresses.

**Notes.**

- A key-based wallet is an off-chain instrument of control with no on-chain representation; what is observable on chain are the addresses it controls and the transactions it signs.
- A wallet typically controls many addresses under the UTXO model, where new addresses are generated per receipt, and one or few under the account model. The relation between a wallet and the addresses it controls is generally not observable and must be inferred (§7).
- In vendor and investigative usage "wallet" is frequently used for a set of addresses inferred to be under common control. This vocabulary reserves `cluster` (§7.3) for that meaning: a cluster is an inference about a wallet, not the wallet itself.
- Smart-contract wallets (multi-signature contracts, account-abstraction accounts) are the exception to the first note: they hold value in an on-chain contract whose transfer rules are code, and the signers the contract authorises take the role of the key holder. They are wallets in the sense of this entry.

**Sources.** Chainalysis, *Crypto Glossary*, "Crypto Wallet" (aligned: a tool that stores the keys used to access and manage crypto-assets).

### 5.2 Custodial wallet

**Identifier.** `custodial-wallet`

**Definition.** A wallet whose private keys are held by a party other than the beneficiary of the value it controls, so that a transfer requires the action of that party.

**Notes.**

- The key-holding party is the *custodian*; the beneficiary has a claim against the custodian rather than on-chain control. Custodians are typically services subject to VASP or CASP obligations.
- On chain, custodial wallets appear as addresses operated by the custodian. Custodians commonly assign each beneficiary a *deposit address* and sweep received value into pooled addresses that hold the value of many beneficiaries. A deposit address may therefore be associated with one beneficiary, a pooled address with none in particular.
- The test is control over the keys, not the legal form of the relationship. This follows the FATF approach, under which a service that holds or controls private keys on behalf of others provides custody.

**Sources.** FATF (2021), *Updated Guidance for a Risk-Based Approach to Virtual Assets and Virtual Asset Service Providers*; Regulation (EU) 2023/1114 (MiCA), Art. 3(1)(17) ("providing custody and administration of crypto-assets on behalf of clients"); Chainalysis, *Crypto Glossary*, "Crypto Wallet".

### 5.3 Non-custodial wallet

**Identifier.** `non-custodial-wallet`

**Definition.** A wallet whose private keys are held by the beneficiary of the value it controls, so that no other party can transfer that value.

**Notes.**

- Also called a *self-custodial* wallet, a *self-hosted* wallet or address (Regulation (EU) 2023/1113), or, in FATF usage, an *unhosted* wallet. Hardware wallets, mobile and browser wallets, and paper keys are non-custodial when the beneficiary alone keeps the keys.
- Multi-signature arrangements in which all key holders are the same party are non-custodial.
- Arrangements that split keys between the beneficiary and a service (e.g., a 2-of-3 scheme with a recovery key held by a provider) are classified by who can complete a transfer alone. Where neither party can, the arrangement is a shared-custody boundary case; its treatment is deferred (§9).

**Sources.** FATF (2021), which uses "unhosted wallet" for this case; Regulation (EU) 2023/1113, Art. 3(20) ("self-hosted address"); Chainalysis, *Crypto Glossary*, "Crypto Wallet".

## 6. Wallet operational roles

Operators of services distinguish their wallets by where signing keys are held and how they are used: online and signing automatically, online but signing under approval, or offline. These are *roles* assigned by the operator's practice, not properties of a technology, and they are typically inferred from transaction patterns rather than observed on chain. Intelligence work uses them to interpret flows, for instance sweeps from deposit addresses into a hot wallet, or periodic transfers between hot and cold wallets.

### 6.1 Hot wallet

**Identifier.** `hot-wallet`

**Definition.** A wallet whose signing keys are held on a system connected to a network and that signs transactions automatically, without per-transaction human approval.

**Notes.**

- Hot wallets serve continuous operations such as processing customer withdrawals and consolidating deposits. They carry the highest exposure to key compromise and typically hold a small share of an operator's total balance.
- Connectivity alone does not make a wallet hot; the defining property is automatic signing. Automated checks applied before signing, such as transaction limits or address allow-lists, do not change the role. A wallet that is online but signs only after human approval of each transaction is a warm wallet (§6.2).

**Sources.** Chainalysis, *Crypto Glossary*, "Crypto Wallet" (aligned on connectivity; this entry adds the signing-policy criterion).

### 6.2 Warm wallet

**Identifier.** `warm-wallet`

**Definition.** A wallet whose signing keys are held on a system that can connect to a network, but that signs only after human approval of each transaction.

**Notes.**

- Warm wallets form an intermediate liquidity tier: they replenish hot wallets and receive value from cold wallets. Approval is typically multi-party and combined with automated controls such as transaction limits and address allow-lists.
- No widely shared definition exists; vendor glossaries commonly define hot and cold wallets only. The definition here is a proposal of this vocabulary. It distinguishes warm from hot by whether a person approves each transaction, and warm from cold by whether the signing system can connect to a network.

**Sources.** CryptoCurrency Certification Consortium (C4), *CryptoCurrency Security Standard*, which specifies the key-management controls operators apply to such wallets without naming the roles.

### 6.3 Cold wallet

**Identifier.** `cold-wallet`

**Definition.** A wallet whose signing keys are generated and held on systems that are never connected to a network, so that transactions are signed offline and transferred to a connected system for broadcast.

**Notes.**

- Cold wallets hold the bulk of an operator's balance; movements from them are infrequent and typically large. Infrequent use is characteristic of the role but not part of its definition.
- The role is defined by practice, not by device. A hardware wallet keeps its keys offline, but one attached to a connected host that signs daily operational transactions under human approval plays the warm role (§6.2), not the cold role.

**Sources.** Chainalysis, *Crypto Glossary*, "Crypto Wallet" (aligned: keys kept offline).

## 7. Computational methods

The primitives of §4 are observed; the wallets and roles of §5 and §6 are not, and intelligence work reaches them by inference from on-chain data. The terms in this section name that inference and its result: the process of grouping addresses, the rules the process applies, and the sets of addresses it produces. Keeping these apart from the wallets and actors they are inferences about is the main purpose of the section, since a large share of the disagreement noted in §1 comes from using the result of an inference as if it were an observation.

### 7.1 Address clustering

**Identifier.** `address-clustering`

**Definition.** The process of grouping addresses that are presumed to be controlled by the same party, based on evidence observable on the blockchain and on one or more stated clustering heuristics.

**Notes.**

- Clustering is inference, not observation. Its result is interpretable only together with the heuristics that produced it; intelligence data derived from clustering should therefore state those heuristics (§7.2).
- Under the UTXO model the canonical heuristic is the *multi-input* (or *co-spend*) heuristic: all inputs of a transaction are presumed to be controlled by the same party. It fails for collaborative transactions such as CoinJoin, which are constructed to violate it. The *change-address* heuristic identifies which output of a transaction returns value to the sender and links it to the inputs.
- Under the account model there is no co-spending; clustering rests on other signals, such as the sweeping of deposit addresses into a service's hot wallet (§5.2, §6.1), or funding relations between addresses.

**Sources.** Meiklejohn et al. (2013); Androulaki et al. (2013); Harrigan and Fretter (2016); Chainalysis, *Crypto Glossary*, "Address Clustering" (aligned on the definition).

### 7.2 Clustering heuristic

**Identifier.** `clustering-heuristic`

**Definition.** A rule that infers common control of two or more addresses from a pattern in on-chain data.

**Notes.**

- Every heuristic has conditions under which the inferred control does not hold. A heuristic is sufficiently specified for exchange only if these failure conditions are stated together with the rule; a statement of heuristics as called for in §7.1 should include them.
- A name alone does not identify a heuristic, since implementations under the same name differ in detail, for instance in whether transactions with CoinJoin structure are excluded from the multi-input heuristic, or which output patterns the change-address heuristic accepts. Exchanged clustering results should therefore identify the implementation or reference specification of each heuristic applied (§9).

**Sources.** Meiklejohn et al. (2013) §4; Harrigan and Fretter (2016).

### 7.3 Cluster

**Identifier.** `cluster`

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

The [Bitcoin developer glossary](https://developer.bitcoin.org/glossary.html) and the [ethereum.org glossary](https://ethereum.org/en/glossary/) define the technical primitives precisely for their respective chains. They are among the sources for §4 but are silent on custody, operational roles, and clustering, which are concerns of intelligence work rather than of protocol design.

### 8.3 Regulatory definitions

The FATF *Updated Guidance for a Risk-Based Approach to Virtual Assets and Virtual Asset Service Providers* (2021) and Regulation (EU) 2023/1114 (MiCA) define custody in terms of holding or controlling private keys on behalf of others. For the non-custodial case FATF uses "unhosted wallet" and Regulation (EU) 2023/1113 "self-hosted address". §5 follows these definitions and references them rather than restating them; the legal categories VASP and CASP themselves are out of scope.

### 8.4 INTERPOL DW-VA-Taxonomy and GraphSense conventions

The [INTERPOL Darkweb and Virtual Assets Taxonomy](https://interpol-innovation-centre.github.io/DW-VA-Taxonomy/) (Entity Taxonomy v0.3, Abuse Taxonomy v0.1) classifies actors and abuses in the dark-web and cryptoasset ecosystems, and GraphSense [TagPacks](https://github.com/graphsense/graphsense-tagpacks/wiki/GraphSense-TagPacks) reuse it for their `category` and `abuse` fields. Both are classification schemes rather than vocabularies. They are the expected starting point for the actor and abuse classification work that earlier revisions of this document contained and that is now deferred (§9).

## 9. Open issues

- **Actor and abuse classification.** Earlier revisions of this document specified Actor Type and Abuse Type concept schemes with an extension mechanism. That material is withdrawn from this document and will be reintroduced as a separate OBIS classification document built on this vocabulary. Until then OBIS specifies no actor or abuse categories.
- **Further terms.** Candidates for later revisions include *actor*, *entity*, *service*, *attribution*, *label*, *transaction graph*, *mixer* and *CoinJoin*, and *bridge*. Proposals go to the discussion thread linked in the status block.
- **Shared custody.** Arrangements in which neither the beneficiary nor a service can complete a transfer alone (§5.3) are not yet classified.
- **Heuristic specifications.** Reference specifications of individual clustering heuristics, including their failure conditions, are planned as separate OBIS work.
- **Serialisation.** A machine-readable representation of the vocabulary (e.g., SKOS/RDF or JSON) is deferred; the entries in this document are the normative representation.
- **Multilingual labels.** Labels in languages other than English are deferred.

## References

- IETF [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119), *Key words for use in RFCs to Indicate Requirement Levels*.
- IETF [RFC 8174](https://www.rfc-editor.org/rfc/rfc8174), *Ambiguity of Uppercase vs Lowercase in RFC 2119 Key Words*.
- S. Nakamoto, [*Bitcoin: A Peer-to-Peer Electronic Cash System*](https://bitcoin.org/bitcoin.pdf), 2008.
- G. Wood, [*Ethereum: A Secure Decentralised Generalised Transaction Ledger*](https://ethereum.github.io/yellowpaper/paper.pdf) (Yellow Paper).
- Bitcoin, [BIP-13](https://github.com/bitcoin/bips/blob/master/bip-0013.mediawiki), *Address Format for pay-to-script-hash*.
- Bitcoin, [BIP-173](https://github.com/bitcoin/bips/blob/master/bip-0173.mediawiki), *Base32 address format for native v0-16 witness outputs*.
- Bitcoin, [BIP-341](https://github.com/bitcoin/bips/blob/master/bip-0341.mediawiki), *Taproot: SegWit version 1 spending rules*.
- Bitcoin, [BIP-350](https://github.com/bitcoin/bips/blob/master/bip-0350.mediawiki), *Bech32m format for v1+ witness addresses*.
- Ethereum, [EIP-55](https://eips.ethereum.org/EIPS/eip-55), *Mixed-case checksum address encoding*.
- Ethereum, [EIP-1014](https://eips.ethereum.org/EIPS/eip-1014), *Skinny CREATE2*.
- Bitcoin Project, [*Developer Glossary*](https://developer.bitcoin.org/glossary.html).
- Ethereum Foundation, [*Ethereum Glossary*](https://ethereum.org/en/glossary/).
- FATF, [*Updated Guidance for a Risk-Based Approach to Virtual Assets and Virtual Asset Service Providers*](https://www.fatf-gafi.org/en/publications/Fatfrecommendations/Guidance-rba-virtual-assets-2021.html), October 2021.
- European Union, [Regulation (EU) 2023/1114 on markets in crypto-assets (MiCA)](https://eur-lex.europa.eu/eli/reg/2023/1114/oj).
- European Union, [Regulation (EU) 2023/1113 on information accompanying transfers of funds and certain crypto-assets (Transfer of Funds Regulation)](https://eur-lex.europa.eu/eli/reg/2023/1113/oj).
- CryptoCurrency Certification Consortium (C4), [*CryptoCurrency Security Standard (CCSS)*](https://cryptoconsortium.org/ccss/).
- Chainalysis, [*Crypto Glossary*](https://www.chainalysis.com/glossary/).
- S. Meiklejohn et al., [*A Fistful of Bitcoins: Characterizing Payments Among Men with No Names*](https://doi.org/10.1145/2504730.2504747), Proc. IMC 2013.
- E. Androulaki et al., [*Evaluating User Privacy in Bitcoin*](https://doi.org/10.1007/978-3-642-39884-1_4), Proc. Financial Cryptography and Data Security 2013.
- M. Harrigan and C. Fretter, [*The Unreasonable Effectiveness of Address Clustering*](https://arxiv.org/abs/1605.06369), 2016.
- INTERPOL Innovation Centre, [*Darkweb and Virtual Assets Taxonomy*](https://interpol-innovation-centre.github.io/DW-VA-Taxonomy/). Entity Taxonomy v0.3, Abuse Taxonomy v0.1.
- GraphSense, [*TagPacks Wiki*](https://github.com/graphsense/graphsense-tagpacks/wiki/GraphSense-TagPacks).
- [OBIS-0001]({{< relref "OBIS-0001" >}}), *OBIS Document Lifecycle*.
