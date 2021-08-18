---
eip: xyz
title: Add Credential Provider related methods to the JSON-RPC
author: Oliver Terbu (@awoie)
discussions-to: https://github.com/ethereum/EIPs/issues/TBD
status: Draft
type: Standards Track
category: Interface
created: 2021-08-18
---

<!--You can leave these HTML comments in your merged EIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new EIPs. Note that an EIP number will be assigned by an editor. When opening a pull request to submit your EIP, please use an abbreviated title in the filename, `eip-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the EIP.-->
Add new methods to the JSON-RPC for storing, creating and proving control of offchain- and onchain credentials under a new `creds_*` prefix.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->

<!-- example copied from eip2844
This EIP describes five new methods to add to the JSON-RPC that enables wallets to support *Decentralized Identifiers* (DIDs) as well as *JSON Object Signing and Encryption* (JOSE) and *CBOR Object Signing and Encryption* (COSE). These standards enables wallets to support data decryption as well as authenticated data, both in standard formats using JOSE and COSE. With these new methods apps can request the DID from a users wallet, from which a DID document can be resolved. The DID document contains public keys that can be used for encryption and signature verification. This enables Alice to discover Bobs public keys by only knowing Bobs DID. This EIP does not enforce the user of any particular DID method or JOSE/COSE algorithms, wallets are free to implement these however they wish.
-->

## Motivation
<!--The motivation is critical for EIPs that want to change the Ethereum protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the EIP solves. EIP submissions without sufficient motivation may be rejected outright.-->

<!-- example copied from eip2844
There has been one main previous effort ([#130](https://github.com/ethereum/EIPs/issues/130), [#1098](https://github.com/ethereum/EIPs/pull/1098)) to add decryption to Ethereum wallets in a standard way. This previous approach used a non standard way to encode and represent data encrypted using `x25519-xsalsa20-poly1305`. While this approach does provide a functional way to add encryption support to wallets, it does not take into account similar work that has gone into standardizing the way encrypted data is represented, namely using [JOSE](https://datatracker.ietf.org/wg/jose/documents/) and [COSE](https://datatracker.ietf.org/wg/cose/documents/). Both of these are standards from IETF for representing signed and encrypted objects. Another shortcoming of the previous approach is that it's impossible to retrieve the `x25519` public key from another user if only an Ethereum address is known. Public key discoverability is at the core of the work that is happening with the [W3C DID standard](https://w3c.github.io/did-core), where given a DID a document which contains public keys can always be discovered. Implementations of this standard already exist and are adopted within the Ethereum community, e.g. [`did:ethr`](https://github.com/decentralized-identity/ethr-did-resolver/) and [`did:3`](https://github.com/3box/3id-resolver). Interoperability between JOSE/COSE and DIDs [already exists](https://github.com/decentralized-identity/did-jwt), and work is being done to [strengthen it](https://github.com/decentralized-identity/did-jose-extensions). Adding support for JOSE/COSE and DIDs will enable Ethereum wallets to support a wide range of new use cases such as more traditional authentication using JWTs, as well as new emerging technologies such as [Secure Data Stores](https://identity.foundation/secure-data-store/) and [encrypted data in IPFS](https://github.com/ipld/specs/pull/269).
-->

## Specification
<!-- example copied from eip2844
Five new JSON-RPC methods are specified under the new `did_*` prefix.
-->

### Store

<!-- example copied from eip2844
Authenticate the current rpc connection to the DID methods.

Prompt the user to give permission to the current connection to access the user DID and the given `paths`.
-->

##### Method: 

`creds_store`

##### Params:

<!-- example copied from eip2844
* `paths` - an array of strings
-->

##### Returns:

<!-- example copied from eip2844
* `did` - the DID which authentication was given for
* `paths` - the paths which was given permission for
-->

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

<!-- example copied from eip2844
This EIP chooses to rely on DIDs and JOSE/COSE since there is already support for these standards in many places, by current systems and new systems. While most systems today rely on JOSE, support for COSE is also specified because it has growing support due to it's compact encoding of data. By using DIDs and JOSE/COSE wallet implementers can also choose which signing and encryption algorithms that they want to support, since these formats are faily agnostic to specific crypto implementations.

### Permission system

A simple permission system is proposed where clients can request permissions though path prefixes, e.g. `/some/permission`. When decryption of a JWE/CWE is requested the wallet should check if the decrypted payload contains a `paths` property. If this property doesn't exist the user may be prompted to confirm that the given rpc connection (app) is allowed to read the decrypted data. If the `paths` property is present in the decrypted data it should contain an array of string paths. If one of the these path prefixes matches with one of the path prefixes the user has already granted permission for then decryption should happen automatically without any user confirmation.

This simple permission system was inspired by some previous comments ([1](https://github.com/ethereum/EIPs/issues/130#issuecomment-329770999), [2](https://medium.com/@wighawag/3-proposals-for-making-web3-a-better-experience-974f97765700)) but avoids data lock in around origins.
-->

## Implementation
<!--The implementations must be completed before any EIP is given status "Final", but it need not be completed before the EIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->

<!-- example copied from eip2844
[IdentityWallet](https://github.com/3box/identity-wallet-js/): A partial implementation of the `did_*` methods using the 3ID DID.

[js-did](https://github.com/ceramicnetwork/js-did): A small library which consumes the `did_*` methods.

[MinimalCipher](https://github.com/digitalbazaar/minimal-cipher): An implementation of DID related encryption for JWE.
-->

## Security Considerations
<!--All EIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. EIP submissions missing the "Security Considerations" section will be rejected. An EIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->

<!-- example copied from eip2844
Both JOSE/COSE and DIDs are standards that have gone though a lot of scrutiny. Their security will not be considered in this document. In the specification section, recommendations are given for which algorithms to use. For signatures `secp256k1` is already used by ethereum and for decryption `xchacha20poly1305` is widely available, very performant, and already used in TLS.

The main security consideration of this EIP is the suggested permission system. Here various threat models could be considered. However, this EIP does not go into details about how it should work other than suggesting an approach. In the end it is up to wallet implementations to choose how to ask their users for consent.
-->
## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).