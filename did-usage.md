# Impervious DID Spec

This specification describes how Impervious is implementing [Decentralized Identifiers](https://www.w3.org/TR/did-core/), [DIDComm Messaging](https://identity.foundation/didcomm-messaging/spec/), and other DID concepts in the Impervious Browser.

## Service Endpoint Examples

The DID specification is highly adaptable and generalized to support many use cases. Many of these use cases revolve around the use of [service endpoints](https://www.w3.org/TR/did-core/#services). See this example below:

```
{
"service": [
    {
      "id":"did:example:123#foo",
      "type": "LinkedDomains",
      "serviceEndpoint": {
        "origins": ["https://foo.example.com", "https://identity.foundation"]
      }
    },
    {
      "id":"did:example:123#bar",
      "type": "LinkedDomains",
      "serviceEndpoint": "https://bar.example.com"
    }
  ]
}

```

This list of services are two ways of claiming ownership of domains via service endpoints inside of a DID document.

- `id`: a URI referring to the name of this service, for readability and accessibility. This can be set to anything unique to the document.
- `type`: The type of service endpoint this is. In this example, implementors are to understand that a service endpoint with `type` set to `LinkedDomains` means that the DID is claiming ownership over the domains in the `serviceEndpoint` field.
- `serviceEndpoint`: The URI or object describing what or where the subject is. In this case, the `LinkedDomain` service endpoint `type` is specifying `https://foo.example.com`, `https://identity.foundation`, and `https://bar.example.com` as the domains being linked.


The concept of service endpoints is the basis for much of what this document describes below.


## Bitcoin Service Endpoints

This section describes how to express Bitcoin-related concepts into Decentralized Identifiers through the use of service endpoints. Including a bitcoin-related service endpoint into a DID document can be implied to mean that the owner of the DID is willing to receive Bitcoin through the service endpoints. While it does not require a given service endpoint to belong to the owner of the DID, it SHOULD be understood that owner is directing others to the service endpoints as ways to pay or communicate with the owner.

### Keysend / AMP

[Keysend](https://github.com/lightning/blips/blob/master/blip-0003.md) is a method to push Bitcoin through to a Lightning node by specifying just the public key of the node. AMP (TBD specification) is an improvement to that but accomplishes a similar goal.

By including this service, the owner of the DID is specifying that this lightning public key is available to receive lightning payments via Keysend/AMP.

- `type` MUST be set to `LNPubkey`
- `serviceEndpoint` MUST be set to a colon-deliminated string that first consist of `lightning` as the prefix followed by the 32 byte lightning public key of the node.

Example:

```
{
"service": [
    {
      "id":"did:example:123#LNPubkey",
      "type": "LNPubkey",
      "serviceEndpoint": "lightning:abc123..."
    }
  ]
}

```

### LNURL

[LNURL](https://github.com/fiatjaf/lnurl-rfc) is a specification for interacting with Lightning via URL's. LNURL strings can direct users to pay, receive, log in, open channels, and many other protocols.

By including this service, the owner of the DID is specifying that they are able to be interacted with based on the contents of the LNURL string. The LNURL protocol type will be encoded in the string. Multiple LNURL services may be listed to include multiple LNURL methods.

- `type` MUST be set to `LNURL`
- `serviceEndpoint` MUST be set to a colon-deliminated string that first consists of `lightning` as the prefix followed by the LNURL encoded string.

Example:

```
{
"service": [
    {
      "id":"did:example:123#lnurl",
      "type": "LNURL",
      "serviceEndpoint": "lightning:LNURL..."
    }
  ]
}
```

### Bolt12 Offers

[Bolt12 Offers](https://bolt12.org) are improved static invoices that enable ad hoc payments through a generic, non-amount-dependent bolt12 payment string.

By including this service, the owner of the DID is specifying that they are able to accept funds from others that pay to the specified payment string.

- `type` MUST be set to `LNOffers`
- `serviceEndpoint` MUST be set to a colon-deliminated string that first consists of `lightning` as the prefix followed by the bolt12 payment string.

Example:

```
{
"service": [
    {
      "id":"did:example:123#LNOffers",
      "type": "LNOffers",
      "serviceEndpoint": "lightning:lno1qcp...."
    }
  ]
}
```

### Custodians

The protocol for specifying payments to 3rd parties will be implementation specific to the given custodian. For instance, the Bitcoin custodian Strike has an API for retrieving invoices from a specific user on their platform. This allows others to pay the custodian and specify that it was meant for a given user.

- `type` MUST be set to a string that the custodian has delegated as their DID service endpoint type. Until a common protocol for paying custodians is created, there could be many different implementations.
- `serviceEndpoint` MUST be set to a URI specifying through what method the payment information for a given user can be retrieved.

Example:

```
{
"service": [
    {
      "id":"did:example:123#Strike",
      "type": "Strike",
      "serviceEndpoint": "https://strike.me/username"
    }
  ]
}
```

### BIP47 / Paynyms

[Paynyms](https://paynym.is) allow on-chain payments to a pseudonymous username. A new address is generated using [BIP47](https://github.com/bitcoin/bips/blob/master/bip-0047.mediawiki) each time a payment is made to preserve on-chain privacy and transaction security.

There are two options for interacting with paynyms. By including a BIP47 service endpoint, the owner of the DID is specifying the payment code needed for others to pay to new addresses belonging to the owner. By including a Paynym service endpoint, the owner is specifying the directory service that resolves usernames to BIP47 payment codes. 

BIP47:
- `type` MUST be set to `BIP47`
- `serviceEndpoint` MUST be set to a colon-deliminated string that first consists of `BTC` as the prefix followed by the BIP47 payment code.

Paynym:
- `type` MUST be set to `Paynym`
- `serviceEndpoint` MUST be set to a URI specifying through what method the BIP47 payment code for a given nym can be retrieved.


Example:
```
{
"service": [
    {
      "id":"did:example:123#BIP47",
      "type": "BIP47",
      "serviceEndpoint": "BTC:PM8T..."
    },
    {
      "id":"did:example:123#Paynym",
      "type": "Paynym",
      "serviceEndpoint": "https://paynym.is/+username"
    }
  ]
}
```

### Onchain

A static Bitcoin address may be listed as a way to receive payments. Consider the privacy implications of reusing & publicly displaying a Bitcoin address. This should be mainly a fallback option.

- `type` MUST be set to `Bitcoin`
- `serviceEndpoint` MUST be set to a colon-deliminated string that first consists of `BTC` as the prefix followed by the Bitcoin address.

Example:
```
{
"service": [
    {
      "id":"did:example:123#Bitcoin",
      "type": "Bitcoin",
      "serviceEndpoint": "BTC:bc1..."
    }
  ]
}
```

### Bitcoin Payment Metadata

For each of the many service endpoint payment types, additional optional metadata can be added to specify preferences for the receiver. It's important to note that these are owner specified preferences and are not enforced.

#### Priority

When listing multiple payment methods, the owner of a DID can specify their preferences by including a priority field to each service endpoint.

- `priority` is an optional key that MUST be set to a numerical value where the highest priority is 1 and the lowest is any number following it.

Example:
```
{
"service": [
    {
      "id":"did:example:123456789abcdefghi#LNPubkey",
      "type": "LNPubkey",
      "priority": 1,
      "serviceEndpoint": "lightning:abc123..."
    },
    {
      "id":"did:example:123456789abcdefghi#BIP47",
      "type": "BIP47",
      "priority": 2,
      "serviceEndpoint": "BTC:PM8T..."
    },
    {
      "id":"did:example:123456789abcdefghi#Bitcoin",
      "type": "Bitcoin",
      "priority": 3,
      "serviceEndpoint": "BTC:bc1..."
    }
  ]
}
```


#### Min / Max

The owner of a DID can also specify their preferences for amounts when receiving payments through multiple service endpoints.

- `minAmountSat` is an optional key that MUST be set to a numerical value representing the minimum amount of satoshis that payers should send when interacting with the given service endpoint.
- `maxAmountSat` is an optional key that MUST be set to a numerical value representing the maximum amount of satoshis that payers should send when interacting with the given service endpoint.


Example:
```
{
"service": [
    {
      "id":"did:example:123456789abcdefghi#LNPubkey",
      "type": "LNPubkey",
      "maxAmountSat": 1000000,
      "serviceEndpoint": "lightning:abc123..."
    },
    {
      "id":"did:example:123456789abcdefghi#BIP47",
      "type": "BIP47",
      "minAmountSat": 1000000,
      "serviceEndpoint": "BTC:PM8T..."
    },
    {
      "id":"did:example:123456789abcdefghi#Bitcoin",
      "type": "Bitcoin",
      "minAmountSat": 1000000,
      "maxAmountSat": 5000000,
      "serviceEndpoint": "BTC:bc1..."
    }
  ]
}

```


## DID Communication

This section includes information about how to communication between DID owners can occur.

### Lightning

Several Lightning based communications applications rely on Keysend/AMP to attach messages to the payment TLV records. Those can be specified as a service endpoint.

- `type` MUST be the lightning based communication implementation (Sphinx chat, impervious, etc.)
- `serviceEndpoint` MUST be set to a colon-deliminated string that first consist of `lightning` as the prefix followed by the 32 byte lightning public key of the node.


Example:
```
{
"service": [
    {
      "id":"did:example:123#LNPubkey",
      "type": "LNPubkey",
      "serviceEndpoint": "lightning:abc123..."
    },
    {
      "id":"did:example:123#Sphinx",
      "type": "Sphinx",
      "serviceEndpoint": "did:example:123#LNPubkey"
    },
    {
      "id":"did:example:123#Impervious",
      "type": "Impervious",
      "serviceEndpoint": "did:example:123#LNPubkey"
    }
  ]
}
```

### DIDComm

[DIDComm](https://identity.foundation/didcomm-messaging/spec/) is a secure, private communications layer built on top of the decentralized properties of a DID.

By including this service, the owner of the DID is specifying that they are listening to a given transportation method and can understand DIDComm based messages. Multiple service endpoints can be specified for resiliency and redundancy.

- `type` MUST be `DIDCommMessaging`
- `serviceEndpoint` MUST be set to a URI specifying through what method & transport messages can be sent to.


Example:
```
{
"service": [
    {
      "id":"did:example:123#LNPubkey",
      "type": "LNPubkey",
      "serviceEndpoint": "lightning:abc123..."
    },
    {
      "id": "did:example:123#didcomm-1",
      "type": "DIDCommMessaging",
      "serviceEndpoint": "http://example.com/path",
    },
    {
      "id": "did:example:123#didcomm-2",
      "type": "DIDCommMessaging",
      "serviceEndpoint": "did:example:123#LNPubkey",
    }
  ]
}
```


#### DIDComm Mediators

[DIDComm Mediators](https://identity.foundation/didcomm-messaging/spec/#routing) may be used to handle DIDComm on behalf of another individual. DID owners can delegate communication to another DID by linking the mediator DID. E2EE SHOULD be used to ensure mediators cannot see the contents of the message.

Example:

```
{
"service": [
    {
      "id": "did:example:123#didcomm-1",
      "type": "DIDCommMessaging",
      "serviceEndpoint": "did:example:somemediator",
    },
  ]
}
```

## Supporting DID Ecosystem

A variety of supporting DID specifications can be utilized in the management and resolution of DIDs.

### Well Known DID

[Well Known DID](https://www.ietf.org/archive/id/draft-mayrhofer-did-dns-05.txt) allows for DIDs to be specified in DNS records. This allows for interacting with rememberable website URLs instead of long DID text strings.


Example:
```
_did.example.net.  IN URI 100 10 "did:example:1234abcd"
```


## Examples

### Complete

The following example shows a DID document utilizing all of the payment & communication service endpoints featured in this technical specification.

```
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
  ],
  "id": "did:example:123456789abcdefghi",
  "service": [
    {
      "id": "did:example:123456789abcdefghi#blog",
      "type": "LinkedDomains",
      "serviceEndpoint": "https://myserver.com"
    },
    {
      "id":"did:example:123456789abcdefghi#LNPubkey",
      "type": "LNPubkey",
      "priority": 1,
      "maxAmountSat": 1000000,
      "serviceEndpoint": ["lightning:abc123...", "lightning:xyz987..."]
    },
    {
      "id":"did:example:123456789abcdefghi#lnurl-pay",
      "type": "LNURL-Pay",
      "serviceEndpoint": "lightning:LNURL..."
    },
    {
      "id":"did:example:123456789abcdefghi#LNOffers",
      "type": "LNOffers",
      "serviceEndpoint": "lightning:lno1qcp...."
    },
    {
      "id":"did:example:123456789abcdefghi#BIP47",
      "type": "BIP47",
      "priority": 2,
      "serviceEndpoint": "BTC:PM8T..."
    },
    {
      "id":"did:example:123456789abcdefghi#Paynym",
      "type": "Paynym",
      "priority": 2,
      "serviceEndpoint": "https://paynym.is/+username"
    },
    {
      "id":"did:example:123456789abcdefghi#Bitcoin",
      "type": "Bitcoin",
      "priority": 3,
      "serviceEndpoint": "BTC:bc1..."
    },
    {
      "id":"did:example:123456789abcdefghi#Strike",
      "type": "Strike",
      "priority": 4,
      "serviceEndpoint": "https://strike.me/username"
    },
    {
      "id":"did:example:123456789abcdefghi#Sphinx",
      "type": "Sphinx",
      "serviceEndpoint": "did:example:123#LNPubkey"
    },
    {
      "id":"did:example:123456789abcdefghi#Impervious",
      "type": "Impervious",
      "serviceEndpoint": "did:example:123#LNPubkey"
    },
    {
      "id": "did:example:123456789abcdefghi#didcomm-1",
      "type": "DIDCommMessaging",
      "serviceEndpoint": "http://example.com/path",
      "priority": 1,
      "accept": [
         "didcomm/v2",
         "didcomm/aip2;env=rfc587"
       ]
    },
    {
      "id": "did:example:123456789abcdefghi#didcomm-2",
      "type": "DIDCommMessaging",
      "serviceEndpoint": "did:example:123#LNPubkey",
      "priority": 2,
      "minAmountSat": 100,
      "accept": [
         "didcomm/v2",
         "didcomm/aip2;env=rfc587"
       ]
    },
    {
      "id": "did:example:123456789abcdefghi#didcomm-3",
      "type": "DIDCommMessaging",
      "serviceEndpoint": "http://somemediator.com/path",
      "priority": 3,
      "accept": [
         "didcomm/v2",
         "didcomm/aip2;env=rfc587"
       ],
       "routingKeys": ["did:example:somemediator#somekey"]
    }
  ]
}
```
