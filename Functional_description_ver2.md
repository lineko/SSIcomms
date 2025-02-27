# Functional specification

## 1. Context
The application SSIComms consists of a business module, which makes use of an integrated SIP module and SSI module. The SIP module provides the different SIP communication utilities, the SSI module provides the DIDComm and wallet functionalities. Together, they constitute an application that satisfies use cases such as:
1. Identification before and during an internet communications session
2. The principle of verify the verifier
3. Exchange of payment tokens during internet communications sessions

As its SIP mdule, SSIComms uses SYLK Suite and as its SSI module AnimoMobile SDK, a javascript Aries mobile agent. The business module was built on purpose for this project The diagram below illustrates how all of this fits together.

![SSIComms_structure_diagram_ver2](https://user-images.githubusercontent.com/50589812/156152040-dea0e556-24a7-46b6-b836-3aa3f3156a0f.svg)
 
## 2. Business module
This module contains
- the business logic
- a SIP client capable of transporting DIDComm messages.
- the ability to display messages on screen
 
## 2. SIP module
This module is based on SYLK Suite and supports:
- SIP Signaling
- Voice Over IP
- Messaging
- SIP conferencing
- Gateway
- Web conferencing
 
# 4. SSI module

This module supports the following features:
- TypeScript native framework, built for cross-platform mobile development on iOS & Android
- A generic DID resolver interface that support the did:key, did:web and did:sov methods.
- Issuance of Indy credentials using both v1 and v2 of the Issue Credential protocol.
- Proving and verifying of Indy credentials using both v1 and v2 of the Present Proof protocol.
- Issuance of JSON-LD credentials using v2 of the Issue Credential protocol (RFC 0593 for JSON-LD credential exchange).
- Proving and verifying of JSON-LD credentials using DIF Presentation Exchange v1, and Present Proof protocol v2.
- Sign and verify JSON-LD credentials with the Ed25519Signature2018, BbsBlsSignature2020 and BbsBlsSignatureProof2020 signature suites.
- Fully AIP 1.0 and 2.0 compliant
- Mediation of DIDComm messages to hold messages while the mobile device is offline
- Allows to use mediator of choice (currently either AFJ or ACA-Py supported).
- Set of ready to use React Native modules needed for every mobile wallet
- Storage of key to unlock wallet in iOS & Android secure enclave, protected by biometrics or pin code.
- Set up of push notifications with automatic provisioning of mediator to receive push notifications when messages are received when wallet is closed.
- QR Scanner component for connection invitation handling.
- DIDComm deep linking setup so you can open DIDComm links in your wallet.
- Redux Store wrapper to integrate wallet functionality into a Redux store.
- Wallet import & export to local device for backups and recovery (may look into protocol for storing and backups at mediator in the future).

A detailed overview of which Aries features, agent types, protocols and AIP versions the mobile SDK will support see the Aries Mobile SDK - Aries Feature Overview document.
The special needs of SSIComms where it regards the SSI module are outlined in the use cases below.

# 5. Use cases

All of our use cases have in common that an ongoing peer2peer internet communications session using the SIP protocol needs to set up a DIDComm session between the same participants. The following diagram shows how we expect to make this happen for our first use case, “Identification before and during an internet communications session”. Please note that DIDComm by itself, where a session is usually initiated by reading a QR-code, does not fit the circumstances.
  
![SSI_SIP_flowdiagram_no_redirect drawio](https://user-images.githubusercontent.com/50589812/156152246-c9c8711d-f0f5-4ffc-9975-cb2ce517d6f9.svg)

In case of Bob not receiving the first invite on an SSI capable SIP client, he uses a 301 mesage to deflect the invite to another client, which does have this capability:

![SSI_SIP complex diagram drawio(1)](https://user-images.githubusercontent.com/50589812/156163945-dde7a96e-e15c-4cdf-b67e-10f6352a3133.svg)

For the sake of simplicity, in both cases the proxy servers almost always present in real life situations are not shown here.

In our second use case, “Verify the verifier”, Alice and Bob take the role of holder and verifier respectively, and then the role of verifier and holder. The exchange of VC’s twice can of course be achieved by using existing DIDComm messages, but that has its drawbacks: not only does the holder have to respond to multiple on screen messages, which does not make for a great user experience, it also misses the opportunity to offload the decision making to the machine layer. Involving more human decision making creates additional risk of the agreed exchange going wrong.

The third use case “Exchange of payment tokens during sessions” concerns the decentralized equivalent of premium rate numbers. Benefitting from their embedded digital wallets, users can identify and pay each other directly using a payment token. 
This use case falls under the category of nice to have, since in spite of its practical relevance, it is only indirectly connected to to the subject of this project.


# 6. Security

From my notes: eventually we are to mention the cryptographic primitives that are being used, together with an explanation of why these were chosen. At this stage however, they will accept just some high level reference. The text below is copied from wikipedia, if we can do better that would be great.

### 6.1. SIP
#### 6.1.1. SIP messages
Concerns about the security of calls via the public Internet have been addressed by encryption of the SIP protocol for secure transmission. The URI scheme SIPS is used to mandate that SIP communication be secured with Transport Layer Security (TLS). SIPS URIs take the form sips:user@example.com.
End-to-end encryption of SIP is only possible if there is a direct connection between communication endpoints. While a direct connection can be made via Peer-to-peer SIP or via a VPN between the endpoints, most SIP communication involves multiple hops, with the first hop being from a user agent to the user agent's ITSP. For the multiple-hop case, SIPS will only secure the first hop; the remaining hops will normally not be secured with TLS and the SIP communication will be insecure. In contrast, the HTTPS protocol provides end-to-end security as it is done with a direct connection and does not involve the notion of hops.
#### 6.1.2. SIP text messages
#### 6.1.3. RTP stream
The media streams (audio and video), which are separate connections from the SIPS signaling stream, may be encrypted using SRTP. The key exchange for SRTP is performed with SDES (RFC 4568), or with ZRTP (RFC 6189). When SDES is used, the keys will be transmitted via insecure SIP unless SIPS is used. One may also add a MIKEY (RFC 3830) exchange to SIP to determine session keys for use with SRTP.

### 6.2. DIDComm
#### 6.2.1. Out-of-band messages
Out-of-band messages are traditionally not encrypted, because DIDComm does not do so. However, in this case they are, because they are carried as SIP text mesages. See above to find out how SIP messages are encrypted.

#### 6.2.2. DIDComm messages
In DIDComm v1, the security is loosely based on standard JSON Web encryption. DIDComm v2 uses full JOSE standards (JWM, EDHC-1PU, etc) 


