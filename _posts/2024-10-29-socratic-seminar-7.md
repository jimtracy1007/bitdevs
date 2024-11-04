---
layout: post
type: socratic
title: "Socratic Seminar 7"
---


We will start with introductions, some basic ground rules, and jump into technical discussions. We will cover aspects of the bitcoin protocol, new research developments, recent news, and software developments.
# Announcements
-   No pictures or recordings
-   Thank you [OGBC](https://www.ogbc.com/) and [Lnfi Network](https://www.lnfi.network) for sponsorship of the event space and pizzas
-   Introductions
# Agenda
- Industry Updates, [Bitcoin Projects' Fundraising Updates](https://docs.google.com/spreadsheets/d/1k0I348eU-dEVKTTBVwfyyXozLjDCTqCPszLFGgJ7KOg/edit?usp=sharing) - Darius
- Status & Roadmap for Ark at Second - Steven Roose, CEO, Second


# Industry Updates - Bitcoin News & Releases

#### [Bitcoin Mempool Upgrades"](https://bitcoinops.org/en/newsletters/2024/10/11/#guide-for-wallets-employing-bitcoin-core-28-0)

Recently, there has been some really cool mempool P2P upgrades to Bitcoin that are worth talking about.

- **Package relay for 1P1C (one parent one child).** In Bitcoin transactions, sometimes transactions get "stuck" in the mempool (the pool of pending transactions) because of low fees. A common strategy to unstick a transaction is to create a "child" transaction with a higher fee, linking it to the "parent" transaction to raise the combined fee rate and encourage miners to include it in the block. This is called Child Pays for Parent (CPFP). The one-parent-one-child (1p1c) package relay is an optimization that allows Bitcoin nodes to more efficiently relay transactions using CPFP by accepting packages where a single parent transaction has just one child transaction. This is expected to improve the reliability and speed of transaction confirmation, particularly for users who rely on CPFP to boost fees on low-fee transactions.

- **TRUC transaction relay.** Topologically Restricted Until Confirmation (TRUC) is a policy related to transaction relay in Bitcoin. TRUC transactions are designed to be relayed through the network with certain restrictions that limit how they interact with other unconfirmed transactions. The restriction ensures that TRUC transactions can’t be double-spent or altered until they are confirmed in a block. The goal of TRUC is to enhance security by creating a predictable, orderly structure for how transactions propagate across the network, helping reduce the likelihood of mempool-related attacks.

- **Package RBF.** Package RBF is an enhancement to the existing Replace-by-Fee (RBF) mechanism that allows users to "replace" low-fee transactions with ones that have a higher fee to increase their chances of confirmation. Traditionally, RBF only applied to individual transactions. Package RBF allows users to replace multiple transactions in a "package" at once, which includes a parent transaction and its child transactions. This can be particularly useful for more complex transaction structures (e.g., CPFP setups) where multiple transactions are linked. The benefit of Package RBF is that it enables more flexible fee bumping for transactions that are part of a sequence, improving the chances of quick confirmation for dependent transactions and enhancing control for users sending chains of transactions.

- **Sibling eviction.** Sibling eviction is a new feature that optimizes mempool (the pool of unconfirmed transactions held by each node) management. In the Bitcoin mempool, there can be multiple "sibling" transactions—alternative versions of the same transaction where only one can ultimately be confirmed. With sibling eviction, when a new transaction package enters the mempool and competes with an existing one, nodes can evict sibling transactions that are less likely to be confirmed, keeping only the most viable transaction in the mempool. This can improve the efficiency of mempool usage, reduce clutter, and prioritize transactions with better chances of confirmation, especially during periods of high network congestion.

- **Standard P2A (pay-to-anchor) output script type.** The Pay-to-Anchor (P2A) output type is a new standard script type introduced in Bitcoin Core 28.0. It’s designed specifically for situations that involve anchoring transactions—transactions that “anchor” data or other transactions to the Bitcoin blockchain. P2A outputs can provide a standardized way to create “anchor” transactions, which are commonly used in Layer 2 solutions and protocols like the Lightning Network. Anchors ensure that off-chain transactions or data remain tied to an on-chain transaction, maintaining integrity and traceability. The standardization of the P2A output script type enables more predictable behavior across different node implementations and can make it easier for developers to build tools and protocols that rely on anchored data, enhancing interoperability and reliability in Bitcoin Layer 2 applications.

Gregory Sanders has written a [guide](https://bitcoinops.org/en/bitcoin-core-28-wallet-integration-guide/) for Optech aimed at developers of wallets and other software that uses Bitcoin Core to create or broadcast transactions. The guide walks through the use of several of the features and describes how the features can be useful for multiple protocols, including simple payments and RBF fee bumping, LN commitments and [HTLCs](https://bitcoinops.org/en/topics/htlc/), [Ark](https://bitcoinops.org/en/topics/ark/), and [LN splicing.](https://bitcoinops.org/en/topics/splicing/)

#### [MuSig2 merged libsecp256k1](https://github.com/bitcoin-core/secp256k1/blob/master/examples/musig.c)

libsecp256k1 is a highly optimized C library designed to perform cryptographic operations on the secp256k1 elliptic curve, which is the specific curve Bitcoin uses for its public-key cryptography. Created and maintained by Bitcoin Core developers, this library is critical to the Bitcoin network's efficiency and security.

MuSig2 is a secure n-of-n signing scheme that combines participant keys into a group key and produces Schnorr signatures for the group key.

The merging of MuSig2 into libsecp256k1 is significant because it provides a highly efficient, standardized, and secure implementation of the MuSig2 multi-signature scheme for Bitcoin and other cryptographic applications. 

[Utreexod Beta Is Now Available for General Public Testing](https://groups.google.com/g/bitcoindev/c/5GyV9af9lv4?ref=nobsbitcoin.com)

Utreexod is a full node bitcoin implementation with support for utreexo accumulators. It enables immediate node bootstrap by having the UTXO state hardcoded into the codebase, uses a tiny amount of memory, and has a low disk i/o.

Key features:

- Implementation of efficient Utreexo accumulators with an improved deletionn algorithm from the Utreexo paper.
- Efficient P2P transaction relay with support for caching utreexo proofs for mempool transactions.
- Quick sync to the tip of the blockchain with AssumeUtreexo.
- Built in wallet support (with BDK wallet).
- Electrum personal server support for usage with other wallets.

#### [Spark - A Payment-focused Bitcoin L2](https://x.com/buildonspark/status/1849833055839727821)

Spark is a Bitcoin L2 that enables instant, dirt-cheap, and unlimited self-custodial transactions of bitcoin and tokens while also enabling users to send and receive natively via Lightning. It's open-sourced and secured by Bitcoin.

It's an L2 that tries to solve a lot of the onboarding issues in lightning at the expense of trustlessness. At a high level, it acts as an intermediary "less trusted" layer between Bitcoin and Lightning with the following advantages:

- Native BTC
- Full self-custody
- Instant settlement
- Extremely low fees
- Native tokens (e.g. stablecoins)
- Native Lightning interface **without needing to run a Lightning node**
- Ability to scale to billions of users
- 1/n trust assumptions (or minority/n) with the risk only at the time of transfer, not in perpetuity
- Unconditional unilateral exits
- Capital efficiency (no pre-funding, liquidity lockups, etc.)
- Exists without the need for a new Bitcoin OPcode or any Bitcoin changes (although improves when they're available)

**Statechains Overview**
- Goal: Transfer Bitcoin UTXOs off-chain by creating a jointly controlled UTXO between the user and the Statechain Entity (SE).
- Key Pairing: The user (e.g., Alice) and SE have private keys whose combined key controls the UTXO. This combined key must sign to spend it.

 **Ownership Transfer Process**
- Tweaking SE’s Key: When Alice transfers the UTXO to Bob, SE tweaks its key so that SE and Bob's combined key can sign the same UTXO, invalidating Alice's ability to sign.
- Security via Key Discarding: SE deletes the untweaked key used for Alice, preventing her from collaborating on future transactions.

**Exit Transactions with Timelocks**
- Exit Assurance: Exit transactions allow users to claim the UTXO on-chain if needed, with each owner getting a timelocked exit transaction.
- Decreasing Timelocks: With each transfer, the timelock decreases, ensuring that the latest owner has priority in claiming the UTXO.

**Leaf-Splitting for Flexible Spending**
- Key Splitting for Denominations: UTXOs can be divided into smaller denominations by splitting the key across several branches, allowing finer management without on-chain transactions.
- Ensuring Key Consistency: The sum of keys in each branch matches the original parent key, enabling flexible aggregation when needed.

**Aggregation and Spark Trees**
- Branch and Leaf Structure: Spark trees use branches and leaves to organize UTXOs, removing absolute timelocks and aggregating keys across branches.
- Aggregation of Child Keys: Each child key from a split branch is created to sum up to the parent key, supporting complex key management without on-chain interactions.

#### [OCEAN Unveils Decentralized Alternative Templates for Universal Mining (DATUM)](https://newsdirect.com/news/ocean-restoring-bitcoin-mining-decentralization-177177279)

Similar to Stratum V2, DATUM is designed to decentralize block construction by empowering miners to create their own block templates via their own Bitcoin node. Users can mine on pools that offer Datum support or solo mine without needing a third party to set up a server for them.

The launch of datum is motivated by concerns of large mining pools controlling over 50% of Bitcoin’s hash rate, thus increasing the risk of miner centralization and potential transaction censorship. DATUM aims to decentralize block construction, prevent 51% attacks and keep Bitcoin network free and open.

#### [Lava Loans: Trust-minimized, Bitcoin-Secured Loans](https://delvingbitcoin.org/t/lava-loans-trust-minimized-bitcoin-secured-loans/1112)

A solution that uses bitcoin smart contracts (discreet log contracts) to enable bitcoin-secured loans that offer users cryptographic security rather than exclusively reputational or legal assurances.