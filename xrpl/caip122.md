---
namespace-identifier: xrpl-caip122
title: XRP Ledger Namespace - SIWx
author: Anton Dalgren (@antondalgren)
discussions-to: https://github.com/ChainAgnostic/namespaces/pull/57/
status: Draft
type: Standard
created: 2023-08-09
requires: ["CAIP-122", "CAIP-2", "CAIP-10"]
---

## CAIP-122

For context, see the [CAIP-122](CAIP-122) specification.

## Rationale

This specification provides the signing algorithm to use, the `type` of the signing algorithm to identify it, and a method for signature creation and verification as required by [CAIP-122](CAIP-122).

## Specification

### Signing Algorithm

XRPL uses the ECDSA [secp256k1](secp256k1) or  EdDSA [Ed25519](Ed25519) signing algorithm for signing and verifying messages depending on the type of the keypair that is being used. A [secp256k1](secp256k1) keypair will be using the [secp256k1](secp256k1) algorithm and a [Ed25519](Ed25519) keypair will be using the [Ed25519](Ed25519) algorithm.

Prior to the message being signed using [secp256k1](secp256k1), it needs to be hashed using the SHA-256 algorithm. Correspondingly it needs to be hashed using the SHA-512 algorithm prior to signing it using the [Ed25519](Ed25519) algorithm.

### Signature Type

We propose using the signature type `xrpl:secp256k1` and `xrpl:ed25519` to refer to the chain and algorithm used uniquely.

### Signature Creation

The abstract data model must be converted into a string representation in an unambigious format, and then the string converted to a byte array to be signed over.

We propose the following string format, inspired by [EIP-4361](EIP-4361).

```
${domain} wants you to sign in with your XRPL account:
${address}

${statement}

URI: ${uri}
Version: ${version}
Chain ID: ${chain-id}
Nonce: ${nonce}
Issued At: ${issued-at}
Expiration Time: ${expiration-time}
Not Before: ${not-before}
Request ID: ${request-id}
Resources:
- ${resources[0]}
- ${resources[1]}
...
- ${resources[n]}
```

For example,

```
service.org wants you to sign in with your XRPL account:
r4FTvnahbUfhe1WK2EK5Jz4cNvdFvT8Dzt

I accept the ServiceOrg Terms of Service: https://service.org/tos

URI: https://service.org/login
Version: 1
Chain ID: 0
Nonce: 32891757
Issued At: 2021-09-30T16:25:24.000Z
Resources:
- ipfs://Qme7ss3ARVgxv6rXqVPiikMJ8u2NLgmgszg13pYrDKEoiu
- https://example.com/my-web2-claim.json
```

This message would then be converted to a byte representation of the message, hashed and signed using the proper hashing and signing algorithms as mentioned in the [signing algorithm](#signing-algorithm).

Depending on the method used for signatures, this conversion may or may not happen automatically. For example, if using [ripple-keypairs](ripple-keypairs) you only need to convert it to a byte representation and the library will handle hashing and signing.

### Signature Verification

Signature verification behaves similarly. We can use standard [secp256k1](secp256k1)/[Ed25519](Ed25519) signature verification. We convert the input message to a bytes representation of the string format, then verify the signature over that. The verification public key should be the XRPL wallet address, which is the [XRPL Base58 Encoded](XRPL-Base58-Encoding) encoded Ed25519 public key. These verification methods are also available in the [ripple-keypairs](ripple-keypairs) library.

## References

[eip-4361]: https://eips.ethereum.org/EIPS/eip-4361
[caip-10]: https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-10.md
[caip-2]: https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-2.md
[caip-122]: https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-122.md
[ed25519]: https://ed25519.cr.yp.to/
[secp256k1]: https://en.bitcoin.it/wiki/Secp256k1
[ripple-keypairs]: https://www.npmjs.com/package/ripple-keypairs
[XRPL-Base58-Encoding]: https://xrpl.org/base58-encodings.html

- [EIP-4361](https://eips.ethereum.org/EIPS/eip-4361): Sign-In with Ethereum
- [CAIP-10](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-10.md): Account ID Specification
- [CAIP-2](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-2.md): Blockchain ID Specification
- [CAIP-122](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-122.md): Sign in With X Specification
- [Ed25519](https://ed25519.cr.yp.to/): Ed25519: high-speed high-security signatures
- [secp256k1](https://en.bitcoin.it/wiki/Secp256k1): secp256k1 algorithm.
- [ripple-keypairs](https://www.npmjs.com/package/ripple-keypairs): Ripple keypairs
- [XRPL Base58 Encoding](https://xrpl.org/base58-encodings.html) - Explains how a public key is encoded to a XRPL address.
