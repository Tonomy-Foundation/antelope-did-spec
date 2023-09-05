
# Status of This Document

This document is not a W3C Standard nor is it on the W3C Standards Track. This is a draft document and may be updated, replaced or obsoleted by other documents at any time. It is inappropriate to cite this document as other than work in progress.

# Contributions

The Antelope DID method specification is a fork from the EOSIO DID method specification (Copyright 2021 Gimly B.V.) following the name change of the EOSIO blockchain platform into the Antelope blockchain platform. The name change to Antelope DID and further maintenance and development are now lead by Tonomy Foundation. Comments regarding this document are welcome. Please file issues and PRs directly on Github. Contributors are recognized through adding commits to the code base.

Contributors:

- Jack Tanner | Tonomy Foundation
- Caspar Roelofs | Gimly B.V.
- Jonas Walter
- Julius Rahaus
- Sana Rauf | Block One
- Andres Gomez Ramirez | EOS Costa Rica

<!-- Make sure images have 75 pixel height -->
[![Tonomy Foundation](./assets/tonomy.png)](https://tonomy.foundation)
[![Gimly](./assets/gimly.jpg)](https://gimly.io)
![](./assets/filler.png)
[![Europechain](./assets/europechain.png)](https://europechain.io)
![](./assets/filler.png)
[![Block One](./assets/blockone.png)](https://block.one)
![](./assets/filler.png)
[![EOS Costa Rica](./assets/eoscr.png)](https://eosio.cr)

# Contents

1. [Introduction](#1-introduction)
2. [Design Goals](#2-design-goals)
3. [DID Method Schema: did:antelope](#3-did-method-schema-didantelope)
4. [DID Document](#4-did-document)
5. [Method Operations](#5-method-operations)
6. [Security Considerations](#6-security-considerations)
7. [Privacy Considerations](#7-privacy-considerations)
8. [Reference Implementations](#8-reference-implementations)

# 1. Introduction

## 1.1 Self Sovereign Identity (SSI)

Self sovereign identity is an evolving Internet architecture for how applications store and process identity data.  It is driven by the need for data privacy over personal information.

The architecture has two layers:

1. Identity - the management of accessible public key infrastructure and identifies. [Decentralized Identifiers](https://w3c.github.io/did-core) is the W3C standard that allows this. Compliance with this standard allows application layers to interoperate without a need to understand the base layer decentralised protocols that power identities.
2. Application - use of the identity layer to interact and provide meaningful, secure and verifiable data communications and interaction. The [Verifiable Credentials](https://w3c.github.io/vc-data-model) W3C standard is the most prominent and adopted standard here which is a data structure and message protocol allowing people and organisations to securely and in a verifiable way send and verify information about themselves "credentials" to each other. [DIDComm](https://identity.foundation/didcomm-messaging/spec) is another important application layer that uses DID methods to communicate between SSI identities.

On top of the application layer, use cases in industry can be built which are then interoperable and independent of base protocols. The standards focused heavily on data privacy and security.

More information:

- [SSI Architecture Stack and Community Efforts](https://github.com/decentralized-identity/decentralized-identity.github.io/blob/master/assets/ssi-architectural-stack--and--community-efforts-overview.pdf)
- [Self Sovereign Identity](https://www.identitymanagementinstitute.org/self-sovereign-identity)
- [Self-Sovereign Identity: The Ultimate Beginners Guide!](https://tykn.tech/self-sovereign-identity)

## 1.2 Decentralized Identifiers

[Decentralized Identifiers](https://w3c.github.io/did-core) (DIDs) provide a standard data model to encapsulating unique identifiers and cryptographic material that can be used to interact, verify information from and contact entities. Each decentralised data layer protocol (such as Antelope, Bitcoin, Hyperledger Indy) creates a DID Method Specification (not a W3C standard) which complies to the DID-core W3C standard. This DID Method Specification describes the compliance data model used by the data protocol to encapsulate a unique identifier and the cryptographic material.

## 1.3 Antelope

The Antelope blockchain platform is the next-generation, open-source platform with industry-leading transaction speed and a flexible utility. As a blockchain platform, Antelope is designed for enterprise-grade use cases and built for both public and private blockchain deployments. Antelope is customizable to suit a wide range of business needs across industries with role-based permissions system and secure application transactions processing.

Building distributed applications on Antelope follows familiar development patterns and programming languages used for developing non-blockchain applications. For application developers, familiarity with the development environment results in a seamless user experience as it allows developers to use their preferred development tools.

The Antelope platform provides functionalities such as accounts, authentication, databases, asynchronous communication, and the scheduling of applications across multiple CPU cores and clusters. These functionalities are also common in non-blockchain software development environments.

Some of the groundbreaking features of Antelope include:

1. WebAssembly C++ Compilation
2. High Throughput and Low Latency (0.5s block time)
3. Customizable Resource Model
4. Efficient and Flexible Persistent Data Indexing
5. Support for Human Friendly Account Names
6. Hierarchical Role Based Transparent Permissions
7. Transparent and Synchronous Smart Contract Upgradability
8. Transparent and Synchronous Protocol Upgradability
9. On-chain Governance
10. Efficient Energy Consumption
11. Webauthn and Biometric Hardware Secured Keys Support

## 1.4 Motivation and rationale

The EOSIO technology ecosystem has been adopted by approximately 20 public blockchain is and over 100 private blockchains ([source](https://github.com/eosio-ecosystem/chains)). This includes a wide variety of industry applications.

The growing SSI ecosystem is being adopted by industry and governments alike. Decentralised identifiers are the key layer in the SSI tech stack to be included in this ecosystem. Antelope presents a number of unique advantages for managing self sovereign identity solutions, including its account and key structure and highly flexible governance features. This DID Method Specification allows Antelope to enter this ecosystem through compliance with the standards, bringing the following benefits:

1. Interoperability with the rest of the SSI ecosystem. This allows Antelope based identities to be consumed by governments and industries alike. It provides external interoperability outside of Antelope.
2. Interoperability with other Antelope based identities. Due to the large number of Antelope chains this could be a great way to strengthen the collaboration between all of these projects.
3. Provide transparency of identities through standardisation.
4. Improve security and privacy by bringing the SSI architecture model to Antelope identity systems. This architecture ethically protects human rights while reducing cumbersome data regulation liability from organisations.

## 1.5 Antelope accounts

The Antelope account abstraction is unique within the blockchain industry. There are two features relevant for a DID method:

1. Account names are not bound to cryptographic material. Accounts names are chosen by the creator of the account, which may or may not be the entity that controls the account. Account names are short strings up to 13 characters making them memorisable.
2. Each account can have one or more public-private key pairs which can be used to authorise and assert data about that account. Keys are organised in a hierarchy tree, with human friendly labels for the permission name. Key material can be delegated to another Antelope account. A weighted multi-signature scheme can be used. See [combination.antelope.json](https://github.com/Tonomy-Foundation/antelope-did-spec/blob/master/examples/combination.antelope.json) for an example of a typical Antelope account's key structure that includes both delegated and multi-signature requirements in the hierarchical tree.

This key material and structure needs to be expressed in the "verificationMethod" property of the Antelope DID Document. Numerous conversations have and are still taking place to create a DID compatible method spec. The result of this has been to create a new [verification method](https://w3c.github.io/did-core/#verification-methods) type called [Conditional Proofs](https://github.com/w3c-ccg/verifiable-conditions) which has been drafted and is being reviewed by the W3C Credentials Community Group.

More information:

- [Antelope Accounts and Permissions](https://developers.eos.io/welcome/latest/protocol-guides/accounts_and_permissions)
- [DID core issue 963: Support for threshold multi-sig verificationMethod](https://github.com/w3c/did-core/issues/693)
- [DID core issue 964: Support for delegated verificationMethods](https://github.com/w3c/did-core/issues/694)
- [DID core issue 965: Support for combination of threshold multi-sig and delegated verificationMethod](https://github.com/w3c/did-core/issues/695)
- [DID core - multisig and delegated use case](https://docs.google.com/presentation/d/1vrmdOnN1tiE54e8h7HyegkJUGyrBUITVFNsAVedUwTE)
- [Conditional Proofs (formerly Verifiable Conditions)](https://docs.google.com/document/d/1hxEMQxfNuB6Elmd6V-9bEt0kZqSx-DULycn6CjOpMYs/edit)

## 1.6 Antelope protocol and governance layers

The Antelope protocol is the set of rules within the system cryptographically enforced and historically auditable through a peer to peer network of computer nodes (servers). Fairly unique to the Antelope protocol is that a large degree of these rules are defined in smart contract that live on the blockchain and can be highly-customised and upgraded over time ([source](https://medium.com/coinmonks/difference-between-antelope-software-and-eos-blockchain-13bcc57d1d9d)).

In this way, each Antelope blockchain can have significantly different rules while staying protocol compatible at the peer to peer network layer.

Account creation and updates to account permissions (keys and delegates) are part of the rules defined through smart contracts. This means that different Antelope chains will have different [DID method operations](https://w3c.github.io/did-core/#method-operations) (Create, Read, Update, Deactivate).

To achieve the design goals, the Antelope DID method specification implementation SHOULD be generic and provide the default Antelope method operation CRUD features while also allowing these to be customised by consumers of the implementation (through a constructor or options parameters in function calls). "Implementation" and "consumers" of this implementation will be the terminology used to explain how this design goal is achieved.

In practice, this means that the DID implementations should be imported and the DID create, update and deactivate functions should be overloaded as needed for the Antelope blockchain. This should be done instead of forking the implementations to create more modular software and reduce upstream dependency mismatch issues.

More information:

- [Antelope Consensus Protocol](https://developers.eos.io/welcome/latest/protocol-guides/consensus_protocol
)
- [DID core - multisig and delegated use case](https://docs.google.com/presentation/d/1vrmdOnN1tiE54e8h7HyegkJUGyrBUITVFNsAVedUwTE)

## 1.7 Conformance

The key words MAY, MUST, MUST NOT, OPTIONAL, RECOMMENDED, REQUIRED, SHOULD, and SHOULD NOT in this document are to be interpreted as described in [BCP 14](https://tools.ietf.org/html/bcp14) when, and only when, they appear in all capitals, as shown here.

# 2. Design goals

The design goals of the Antelope DID Method Specification are to:

1. Create a method spec that can be used for all blockchain powered by the unmodified Antelope protocol. Blockchains that have modified the Antelope protocol are not explicitly supported, but may still be compatible and use this method spec if there have not been any incompatible changes to the Antelope protocol such as the account, permissions and key features.
2. Support all relevant and non-depreciated features of Antelope from version 2.0 (time weight permissions are not supported).
3. Support public (EOS, Telos, Europechain, WAX and more), private and hybrid permission Antelope blockchains.
4. Stay as close to the Antelope protocol as possible, do not introduce Antelope chain specific features to the method.
5. Avoid the need for Antelope compatible chains to register separate official DID Methods. Due to the ever expanding number of Antelope chains, this could quickly be a large number and would cause problems to the way DIDs are registered and consumed in the SSI ecosystem.

# 3. DID Method Schema: did:antelope

The Antelope [method-specific DID schema](https://w3c.github.io/did-core/#methods) allows for two distinct method-specific-id schemata.

1. Registered chain name schema
2. Chain-id schema

While one uses the plain chain-id, represented as a generic hash-based identifier for a specific chain, the other utilizes a pre-registered human readable chain name that acts in a similar fashion to domain names and only points toward the actual chain-id. In both cases the chain specific identifier is followed by the name of an account on that chain, separated by a colon.

```
did:antelope:{chain_id/registered_chain_name}:{account-name}
```

Due to the strict requirements registered chain names have to adhere to, a clash with the chain id schema is impossible.

These are the properties that make up an Antelope DID:

- `{registered_antelope_name}` is a pre-registered name of the Antelope chain consisting of one or more colon separated name blocks, each complying to the [Antelope account name type](https://developers.eos.io/welcome/latest/protocol-guides/accounts_and_permissions/#21-account-schema) (one to thirteen lowercase English characters a-z, period . or digits 1-5). This should be registered in the below table and additionally in the [Antelope DID chain method json registry](https://github.com/Tonomy-Foundation/antelope-did-resolver/blob/master/src/antelope-did-chain-registry.json), including at least one service.
- `{account_name}` is the name of the account on the chain, also of [Antelope account name type](https://developers.eos.io/welcome/latest/protocol-guides/accounts_and_permissions/#21-account-schema) type.
- `{chain_id}` is the hash of the genesis block of the chain, expressed in a 64 character string representing a hexadecimal number.

All property schemas are provided with a Regex specification.

## 3.1 Registered chain name schema

```
did:antelope:{registered_antelope_name}:{account_name}
registered_antelope_name = ([a-z][1-5].){1,13}(:([a-z][1-5].){1,13})*
account_name          = ([a-z][1-5].){1,13}
```

e.g. `did:antelope:telos:example`

Each chain name may consist of multiple name blocks, separated by colons. This could be used to represent hierarchical relationships as in domains and subdomains.

e.g. `did:antelope:eos:testnet:jungle:example`

Registered Antelope chain summary:

| Registered Antelope Name | Chain Id |
| ------------- |-------------|
| eos | aca376f206b8fc25a6ed44dbdc66547c36c6c33e3a119ffbeaef943642f0e906 |
| eos:testnet:jungle | 2a02a0053e5a8cf73a56ba0fda11e4d92e0238a4a2aa74fccf46d5a910746840 |
| telos | 4667b205c6838ef70ff7988f6e8257e8be0e1284a2f59699054a018f743b1d11 |
| europechain | f778f7d2f124b110e0a71245b310c1d0ac1a0edd21f131c5ecb2e2bc03e8fe2e |

## 3.2 Chain id schema

```
did:antelope:{chain_id}:{account_name}
chain_id = ([a-z][0-9]){64}
account_name= ([a-z][1-5].){1,13}
```

e.g. `did:antelope:4667b205c6838ef70ff7988f6e8257e8be0e1284a2f59699054a018f743b1d11:example`
<br>
(equivalent to `did:antelope:telos:example`)

## 3.3 DID URLs

### 3.3.1 Path

There is no standardised path schema yet, but there could be in the future.

### 3.3.2 Query

There is no standardised query schema yet, but there could be in the future.

### 3.3.3 Fragments

Fragments are used to dereference a DID URL to a specific [verification method](https://w3c.github.io/did-core/#verification-methods). Fragments for the Antelope DID Method are used to identify the [Antelope permission name](https://developers.eos.io/welcome/latest/protocol-guides/accounts_and_permissions/#3-permissions) primarily. They can be additionally used to specify sub verification methods within a permission with indexes.

The fragment string must begin with the permission name. You can optionally present indexes to look directly into the permission structure. Indexes are unsigned integers starting at 0.

If a fragment is provided and the permission does not exist in the resolved account, an error MUST be thrown. e.g. `#active` framement for an account with no "active" permission.

If a fragment is provided with indexes which do not exist within the permission of the resolved account, an error MUST be thrown. e.g. `#active-1` framement for an account with an "active" permission that only has one key for authorization (not two).

Fragments can be used with either of the DID methods schemas.

```
did:antelope:{chain_id}:{account_name}#permission_name}-{index_1}-{index_2}-...-{index_N}
did:antelope:{registered_antelope_name}:{account_name}#{permission_name}-{index_1}-{index_2}-...-{index_N}
registered_antelope_name = ([a-z][1-5].){1,13}
chain_id              = ([a-z][0-9]){64}
account_name          = ([a-z][1-5].){1,13}
permission_name       = ([a-z][1-5].){1,13}
index                 = (\d+)
```

e.g. `did:antelope:telos:example#active`
<br>
e.g. `did:antelope:4667b205c6838ef70ff7988f6e8257e8be0e1284a2f59699054a018f743b1d11:example#active`
<br>
(both equivalent)

e.g. `did:antelope:telos:example#active-2-0`
<br>
e.g. `did:antelope:4667b205c6838ef70ff7988f6e8257e8be0e1284a2f59699054a018f743b1d11:example#active-2-0`
<br>
(both equivalent)

# 4. DID Document

## 4.1 Identifiers

As described in the [3. DID Method Schema: did:antelope](#3-DID-Method-Schema-didantelope).

### 4.2 DID Subject

The "subject" property does not need to be specified as it is always equal to the DID.

### 4.3 DID Controller

The "controller" property SHOULD NOT be set.
Information regarding antelope top level permission will be included inside the verification methods.

## 4.4 Verification Methods

The verification Methods are populated using information from the Antelope account's permission structure. This can be obtained by requesting the accounts data from any of the API services using the nodeos `get_account` API call.

This permission data is presented in the DID document using the draft ["ConditionalProof2022"](https://github.com/w3c-ccg/verifiable-conditions) type. This new verification type is a current work item by the W3C credentials community group and is expected to become a W3C standard.

### 4.4.1 Account permissions

If the account permission contains a threshold greater than one with one or more keys/delegations with a weight of more than one, then the verification condition MUST use a "conditionWeightedThreshold" property as seen in example [5.1.2 Multi-sig delegated account](#512-multi-sig-delegated-account).

If the account permission contains a threshold greater than one and all weights are 1, then the verification condition MAY use the "conditionThreshold" property instead of "conditionWeightedThreshold".

If the account permission contains a threshold of 1 with more than one key/delegation, then the verification condition MAY use "conditionOr" property instead of "conditionThreshold". An example is seen in [5.1.1 Simple account](#511-simple-account).

If the account permission contains a threshold of 1 with only one keys, then the verification condition MAY use a key type instead of using the ConditionalProof2022 type. If this is done, it MUST use the "EcdsaSecp256k1VerificationKey2019" or "JsonWebKey2020" verification method type as described below in [#4.4.2 Keys](#442-keys).

If the account permission contains a threshold of 1 with only one delegation and no keys, then the verification condition MAY use "conditionDelegated" property instead of "conditionDelegated".

All permissions except the root permission MUST have the "relationshipParent" property sets to the parent property DID URL with fragment.

### 4.4.2 Keys

Antelope uses several key types for authorization. The public key is stored on chain. When an account's information is requested, the keys are part of the permissions array. In Antelope client SDKs, the keys can be read as a single string with a prefix with their type. The SDK and other libraries can be used to extract the relevant material needed to map the key into a verification method object. The table below shows the string prefixes and the corresponding verification method type to use:

| Antelope key prefix | Verification Method type| Key format |
| --- | --- | --- |
| EOS_ (legacy) | EcdsaSecp256k1VerificationKey2019 | publicKeyJwk with "crv" = "secp256k1" |
| PUB_K1_ | EcdsaSecp256k1VerificationKey2019 | publicKeyJwk with "crv" = "secp256k1" |
| PUB_R1_ | JsonWebKey2020 | publicKeyJwk with "crv" = "P-256" |
| PUB_WA_ | JsonWebKey2020 | publicKeyJwk with "crv" = "P-256" |

K1 key types MUST use the official [EcdsaSecp256k1VerificationKey2019](https://w3c-ccg.github.io/lds-ecdsa-secp256k1-2019) material type. The key material MUST be represented with in a Json Web Key with the "publicKeyJwk" property.Example:

```js
{
    "id": "did:antelope:telos:example#active-1",
    "controller": "did:antelope:telos:example",
    "type": "EcdsaSecp256k1VerificationKey2019",
    "publicKeyJwk": {
        "crv": "secp256k1",
        "x": "NtngWpJUr-rlNNbs0u-Aa8e16OwSJu6UiFf0Rdo1oJ4",
        "y": "qN1jKupJlFsPFc1UkWinqljv4YE0mq_Ickwnjgasvmo",
        "kty": "EC"
    }
}
```

The R1 and WA key types both use the [Secp256r1](https://neuromancer.sk/std/secg/secp256r1) (sometimes referred to as P-256) elliptic curve. It is similar to the Secp256k1 curve as explained [here](https://www.johndcook.com/blog/2018/08/21/a-tale-of-two-elliptic-curves).  The R1 and WA key types MUST use the official [JsonWebKey2020](https://w3c.github.io/did-spec-registries/#jsonwebkey2020) material type. The key material MUST be represented with in a Json Web Key with the "publicKeyJwk" property. Example:

```js
{
    "id": "did:antelope:telos:example#active-1",
    "controller": "did:antelope:telos:example",
    "type": "JsonWebKey2020",
    "publicKeyJwk": {
        "crv": "P-256",
        "x": "yLSTsJxPc76EHc0RKdxnT+726KHa7jkgUnHejK3ULSE",
        "y": "6HB9sZjpi69VqpCt68BuHOUadA4aaUf2iPdmuUxFY00",
        "kty": "EC"
    }
}
```

antelope

### 4.4.3 Delegations

If an account permission delegates to another account, a verification method of type ["ConditionalProof2022"](https://github.com/w3c-ccg/verifiable-conditions) MUST be used with the "conditionDelegated" property set to the DID URL of the other Antelope account's verification method corresponding to the delegated permission.

An example is seen in [5.1.1 Simple account](#511-simple-account).

## 4.5 Verification Relationships

The way in which Antelope account permissions are used is not specified in the protocol and cannot be extracted from the Antelope blockchain. It is expected that applications that consume an Antelope DID Method implementation will know more information about how the verification methods are used. This also applies to specific blockchains that consume the implementations.

Consumers of the Antelope DID Method implementation are RECOMMENDED to extend the DID Document's verification relationships with lists of fragments that point to verification methods adequate for relationships.

## 4.6 Services

At least one service SHOULD exist on a DID Document of LinkedDomains type. This can be used to resolve the DID and connect to the Antelope chain through a supported API.

Registered Antelope chain names should add at least one service in the [Antelope DID chain method json registry](https://github.com/Tonomy-Foundation/antelope-did-resolver/blob/master/src/antelope-did-chain-registry.json).

```json
{
    "eos:testnet:jungle": {
        "chainId": "2a02a0053e5a8cf73a56ba0fda11e4d92e0238a4a2aa74fccf46d5a910746840",
        "service": [
            {
                "id": "https://jungle3.cryptolions.io",
                "type": "LinkedDomains",
                "serviceEndpoint": "https://jungle3.cryptolions.io"
            }
        ]
    }
}
```

See the [Antelope DID chain method json registry](https://github.com/Tonomy-Foundation/antelope-did-resolver/blob/master/src/antelope-did-chain-registry.json) for more examples.

## 4.7 Example DID Document

### 4.7.1 Simple account

```json
{
    "@context": ["https://www.w3.org/ns/did/v1",
        "https://raw.githubusercontent.com/Gimly-Blockchain/antelope-did-spec/master/antelope-did-context.json"],
    "id": "did:antelope:telos:example",
    "verificationMethod": [{
        "id": "did:antelope:telos:example#owner",
        "controller": "did:antelope:telos:example",
        "type": "ConditionalProof2022",
        "conditionOr": [
            {
                "id": "did:antelope:telos:example#active-1",
                "controller": "did:antelope:telos:example",
                "type": "EcdsaSecp256k1VerificationKey2019",
                "publicKeyJwk": {
                    "crv": "secp256k1",
                    "x": "NtngWpJUr-rlNNbs0u-Aa8e16OwSJu6UiFf0Rdo1oJ4",
                    "y": "qN1jKupJlFsPFc1UkWinqljv4YE0mq_Ickwnjgasvmo",
                    "kty": "EC"
                }
            }
        ],
    }, {
        "id": "did:antelope:telos:example#active",
        "controller": "did:antelope:telos:example",
        "type": "ConditionalProof2022",
        "conditionOr": [
            {
                "id": "did:antelope:telos:example#active-1",
                "controller": "did:antelope:telos:example",
                "type": "EcdsaSecp256k1VerificationKey2019",
                "publicKeyJwk": {
                    "crv": "secp256k1",
                    "x": "IF/eIukZZ8lixpp57o7vgAm8qN1Z3Nf+6AM67o2o6FS",
                    "y": "VJfQeWtL5LIs5JAri44RvQ77nq5tRg50lvQHZT7smal",
                    "kty": "EC"
                }
            }
        ],
        "relationshipParent": "did:antelope:telos:example#owner",
    }]
}
```

### 4.7.2 Multi-sig delegated account

```json
{
    "@context": ["https://www.w3.org/ns/did/v1",
        "https://raw.githubusercontent.com/Gimly-Blockchain/antelope-did-spec/master/antelope-did-context.json"],
    "id": "did:antelope:telos:example",
    "verificationMethod": [{
        "id": "did:antelope:telos:example#owner",
        "controller": "did:antelope:telos:example",
        "type": "ConditionalProof2022",
        "threshold": 3,
        "conditionWeightedThreshold": [{
                "weight": 1,
                "condition": {
                    "id": "did:antelope:telos:example#owner-0",
                    "controller": "did:antelope:telos:example",
                    "type": "EcdsaSecp256k1VerificationKey2019",
                    "publicKeyJwk": {
                        "crv": "secp256k1",
                        "x": "NtngWpJUr-rlNNbs0u-Aa8e16OwSJu6UiFf0Rdo1oJ4",
                        "y": "qN1jKupJlFsPFc1UkWinqljv4YE0mq_Ickwnjgasvmo",
                        "kty": "EC"
                    }
                }
            }, {
                "weight": 2,
                "condition": {
                    "id": "did:antelope:telos:example#owner-1",
                    "controller": "did:antelope:telos:example",
                    "type": "EcdsaSecp256k1VerificationKey2019",
                    "publicKeyJwk": {
                        "crv": "secp256k1",
                        "x": "IF/eIukZZ8lixpp57o7vgAm8qN1Z3Nf+6AM67o2o6FS",
                        "y": "VJfQeWtL5LIs5JAri44RvQ77nq5tRg50lvQHZT7smal",
                        "kty": "EC"
                    }
                }
            }, {
                "weight": 2,
                "condition": {
                    "id": "did:antelope:telos:example#owner-2",
                    "controller": "did:antelope:telos:example",
                    "type": "ConditionalProof2022",
                    "conditionDelegated": "did:antelope:telos:example2#active"
                }
            }
        ]
    }, {
        "id": "did:antelope:telos:example#active",
        "controller": "did:antelope:telos:example",
        "type": "ConditionalProof2022",
        "relationshipParent": "did:antelope:telos:example#owner",
        "threshold": 1,
        "conditionWeightedThreshold": [{
                "weight": 1,
                "condition": {
                    "id": "did:antelope:telos:example#active-0",
                    "controller": "did:antelope:telos:example",
                    "type": "EcdsaSecp256k1VerificationKey2019",
                    "publicKeyJwk": {
                        "crv": "secp256k1",
                        "x": "ymK3uZFQRP55ZII5eUc4hAxFgLTBy3eWgbllMoBm4nD",
                        "y": "xFgLTBy3eWgbllMoBm4nD9Jqo249Mj_mjjJt6fFUCGI",
                        "kty": "EC"
                    }
                }
            }
        ]
    }]
}
```

# 5. Method Operations

## 5.1 Create

Antelope accounts are created with an on-chain transaction. The default is to call the ["newaccount" action](https://github.com/Antelope/antelope.contracts/blob/52fbd4ac7e6c38c558302c48d00469a4bed35f7c/contracts/antelope.bios/include/antelope.bios/antelope.bios.hpp#L190) on the system contract from an existing account on the blockchain. This action can be changed on each Antelope chain, and upgraded over time. For some Antelope systems, the on-chain account creation process is not openly accessible, and users will use a different mechanism (such as an email and password request to an organisation) to create an account.

Implementations of the Antelope DID Method SHOULD implement the create operation according to the default ["newaccount" action](https://github.com/Antelope/antelope.contracts/blob/52fbd4ac7e6c38c558302c48d00469a4bed35f7c/contracts/antelope.bios/include/antelope.bios/antelope.bios.hpp#L190) defined in the antelope.bios contract. This function SHOULD be polymorphic and can be overridden by a consumer of the implementation. It is recommended that the Antelope DID implementation constructor, or an options parameter can be used to achieve this.

Consumers of a Antelope DID Method implementation SHOULD override the default create behaviour if a different mechanism exists to create an Antelope DID.

## 5.2 Read

Resolution of a DID Document can be done by a service API. This may be an authorised or rate limited API. There are different types of Antelope APIs as outlined in [Service Types](#541-Service-Types).

**Placeholder: Description of method of creating an authorized (permissioned) read request with private Antelope blockchains in mind.**

## 5.3 Update

Antelope account's permissions are updated with an on-chain transaction. The default is to call the ["updateauth" action](https://github.com/Antelope/antelope.contracts/blob/52fbd4ac7e6c38c558302c48d00469a4bed35f7c/contracts/antelope.bios/include/antelope.bios/antelope.bios.hpp#L205) on the system contract from your account. This action can be changed on each Antelope chain, and upgraded over time.

Implementations of the Antelope DID Method SHOULD implement the update operation according to the default ["updateauth" action](https://github.com/Antelope/antelope.contracts/blob/52fbd4ac7e6c38c558302c48d00469a4bed35f7c/contracts/antelope.bios/include/antelope.bios/antelope.bios.hpp#L205) defined in the antelope.bios contract. This function SHOULD be polymorphic and can be overridden by a consumer of the implementation. It is recommended that the Antelope DID implementation constructor, or an options parameter can be used to achieve this.

Consumers of a Antelope DID Method implementation SHOULD override the default update behaviour if a different mechanism exists to update an Antelope DID.

## 5.4 Deactivate

Antelope blockchains do not have a default mechanism to deactivate accounts.

Implementations of the Antelope DID Method SHOULD implement a deactivate function which throws an error. This function SHOULD be polymorphic and can be overridden by a consumer of the implementation. It is recommended that the Antelope DID implementation constructor, or an options parameter can be used to achieve this.

Consumers of a Antelope DID Method implementation SHOULD override the default update behaviour if a different mechanism exists to deactivate an Antelope DID.

[Europechain](https://europechain.io) is an example of Antelope blockchain with a deactivated feature.

# 6. Security considerations

## 6.1 Eavesdropping

For public Antelope blockchains, all transactions, history and stateful information is public.

For private and hybrid Antelope blockchains, access to the data via API or P2P protocol are limited and permission based depending on the blockchain. Private Antelope blockchains SHOULD use encrypted data transport between clients and nodes.

## 6.2 Replay

All Antelope DID method default operations are done through an on-chain transaction are protected by including the blockchain's chain id and a recent block header hash in the transaction (called [Transaction as Proof of Stake](https://antelope.stackexchange.com/questions/2362/what-is-transaction-as-proof-of-stake-tapos-and-when-would-a-smart-contract)).

Antelope DID consumers MAY override the method operations with off-chain mechanisms which may be susceptible to replay attacks. This only applies to permissioned Antelope blockchains.

## 6.3 Message Insertion

All Antelope DID method default operations are done through an on-chain transaction and signed by the accounts authorized private key. There is no way for a party without these keys to act on behalf of the DID.

Note that Antelope accounts may delegate authorisation control to other Antelope accounts on the same blockchain, who are then authorised to send certain transactions on behalf of them. This consent is done through a transaction on chain and must be signed by an existing authorised key of the Antelope account. Delegation can be revoked at any time by the Antelope account.

Antelope DID consumers MAY override the method operations with off-chain mechanisms which may be susceptible to message insertion attacks. This only applies to permissioned Antelope blockchains.

## 6.4 Deletion

Deletion is not a feature of the Antelope P2P protocol.

Functionality to allow users to delete their account can be superimposed over the chain at the API level (such as what is done by Europechain). In such a case, the deletion mechanism will be controlled by such an implementation (for Europechain, deletion is an on-chain transaction signed by the Antelope account's authorized keys)

## 6.5 Modification

All Antelope DID's updated through an on-chainon-chain transaction signed by the accounts authorized private key. There is no way for a party without these keys to act on behalf of the DID.

Note that Antelope accounts may delegate authorisation control to other Antelope accounts on the same blockchain, who are then authorised to send certain transactions on behalf of them. This consent is done through a transaction on chain and must be signed by an existing authorised key of the Antelope account. Delegation can be revoked at any time by the Antelope account.

Antelope DID consumers MAY override the method operations with off-chain mechanisms which may be susceptible to modification attacks. This only applies to permissioned Antelope blockchains.

## 6.6 Man-in-the-Middle

All Antelope DID method default operations are done through an on-chain transaction signed by the accounts authorized private key. An attacker that gains access to a signed transaction message before it is submitted, cannot duplicate this on the same or other chains due to uniqueness in the transaction that is checked for uniqueness by the Antelope protocol. Each transaction has an expiration time, usually set to 30 seconds, and the attacker may choose to delay the submission of this transaction up until the expiration time.

## 6.7 Denial of Service

The ability to limit the service of an Antelope blockchain depends on its infrastructure, governance structure and server API figuration. DID controllers and subjects should be aware of these fundamentals to assess the security of the system.

### 6.7.1 Infrastructure

Infrastructure can be centralised or decentralised.

Blockchains can be configured to have multiple independent organisations that run Antelope peer nodes. More independence block producers running infrastructure increases infrastructure availability reducing service outages and reduces governance attacks (discussed next).

Each node can also be configured to have redundancy capacity through the blockvault_client_plugin. This can be used for centralised blockchains, or for individual block producers in a centralised blockchain.

### 6.7.2 Governance of the blockchain service

The Antelope protocol and DID method is governance agnostic. Antelope blockchains can be Proof of Authority (default), Proof of Stake, Delegated Proof of Stake and more ([source](https://github.com/theblockstalk/antelope-contracts/tree/master/governance)) modifying the system contract.

An Antelope blockchain can be attacked by vulnerabilities in the governance model. Examples:

- block producers that run a Proof of Authority blockchain may be bribed
- block producers slots for a Proof of Stake blockchain can be bought
- block producers slots for a Delegated Proof of Stake blockchain can be bought

If enough of the Antelope blockchain governance is compromised, the blockchain service as a whole may be compromised. DID subjects and controllers should feel comfortable with the governance model of the Antelope blockchain they are using. Antelope blockchains may use internal or external regulation to enforce protection of their governance system.

### 6.7.3 Governance of the blockchain's privileged features

Privileged Antelope account are able to submit transactions to the Antelope blockchain that bypass signature validation checks ([source](https://developers.eos.io/manuals/antelope.contracts/v1.9/key-concepts/system)). The intention of this feature is to be used to administer the blockchain in consensys with all of the operators of the Antelope chain. Such an administration task may be recovered a users account in the event they lose their private key.

Each Antelope chain implements what accounts are privileged and what privileged actions they can perform. This is limited to be accessible only by the current block producers and only in consensus.

By bypassing signature validation checks, privileged accounts can submit transactions on behalf of any other account on the same Antelope blockchain. This includes updating the DID Document. It is for this reason that the governance of the Antelope chain must be trusted.

### 6.7.4 API

If an API service fails completely, a DID will need to find another service to connect to the Antelope blockchain.

## 6.8 Residual Risks

The system's overall security and integrity can only be as good as the DID controller's ability to manage private keys. This is made easier with the ability for wallets to create hierarchies of keys and complex structures. This is still a difficult problem for organizations and people.

# 7. Privacy considerations

## 7.1 Surveillance

In a public Antelope network, all communication is visible by watching the blockchain. In a private network both the peer-to-peer and the node to client communication should be encrypted to ensure data surveillance protection.

## 7.2 Stored data compromise

The DID Document data and history is stored in the blockchain state and can be done through access to a synchronising Antelope blockchain node in the network. In a public network, this can be done through public node APIs or by running a node that synchronises with the public network. A private network has restrictions on the peer-to-peer synchronisation and APIs.

## 7.3 Unsolicited traffic

Create, update and (if supported) deactivate DID operations require transactions signed by the private keys only owned by the DID controller and cannot be forged. Public API nodes do not identify read operations to DIDs, and may receive unsolicited traffic from unintended parties. Public and private APIs do enforce IP or registered API account based rate-limiting to ensure service is not affected by this.

## 7.4 Misattribution

Create, update and (if supported) deactivate DID operations require transactions signed by the private keys only owned by the DID controller and cannot be forged.

## 7.5 Correlation

The DID Document data and history is stored in the blockchain state and can be done through access to a synchronising Antelope blockchain node in the network. State and historic data from interaction of the DID with smart contracts on the blockchain is also accessible through a blockchain node which can be directly correlated with the DID. It is therefore important for DID users to carefully consider what applications they use that have on-chain data to the blockchains.

Private blockchains will ensure that this correlation cannot be done publicly, but can still be done between permissioned and peer-to-peer nodes.

## 7.6 Identification

If personal information is added to the blockchain, potentially a viable credential, this can be permanently accessed through access to a synchronising Antelope blockchain node in the network. With enough identifying information, an identity can be deduced.

For this reason, it is very strongly suggested that personal information is not added to the blockchain, even in private networks.

## 7.7 Secondary use

Antelope blockchain software does not make secondary use of transaction data other than for the purpose of synchronising the blockchain and maintaining the state.

DID users should carefully consider blockchain applications, which may store additional information about the DID, the identity of the DID and other data in off-chain data storage systems. Application providers should clearly state data storage policy and purposes and users should consider this when deciding to use such applications.

## 7.8 Disclosure

DID users of public blockchains should understand that on-chain data is public and does not have limitations on its use or disclosure.

Private blockchains may enforce data privacy restrictions which should be stated and considered by the DID user.

## 7.9 Exclusion

DID users of public blockchains should understand that on-chain data is public and does not have limitations on its use or disclosure.

Private blockchains me support the ability for DID users to control exclusion of their data. This is done on a per-blockchain basis.

# 8. Reference implementations

| Name | Language | Package | Repository |
| --- | --- | --- | --- |
| DID Resolver | Javascript | [npm package](https://www.npmjs.com/package/antelope-did-resolver) | [https://github.com/Tonomy-Foundation/antelope-did-resolver](https://github.com/Tonomy-Foundation/antelope-did-resolver) |
| DID Operations (CRUD) | Javascript |  [npm package](https://www.npmjs.com/package/antelope-did) | [https://github.com/Tonomy-Foundation/antelope-did](https://github.com/Tonomy-Foundation/antelope-did) |
| Universal Resolver Driver | Docker | [Docker Hub](https://hub.docker.com/r/gimlyblockchain/antelope-universal-resolver-driver) | [https://github.com/Tonomy-Foundation/antelope-did-universal-resolver-driver](https://github.com/Tonomy-Foundation/antelope-did-universal-resolver-driver) |
