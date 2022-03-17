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

This section describes how to express Bitcoin-related concepts into Decentralized Identifiers through the use of service endpoints. Including a bitcoin-related service endpoint into a DID document can be implied to mean that the owner of the DID is willing to receive Bitcoin through the service endpoints. While it does not require a given service endpoint to belong to the owner of the DID, it SHOULD be understood that owner is directing others to the service endpoints as ways to pay the owner.

### Keysend / AMP

[Keysend](https://github.com/lightning/blips/blob/master/blip-0003.md) is a method to push Bitcoin through to a Lightning node by specifying just the public key of the node. AMP (TBD specification) is an improvement to that but accomplishes a similar goal.

By including this service, the owner of the DID is specifying that this lightning public key is available to receive lightning payments via Keysend/AMP. Most commonly this will be one of the owner's Lightning nodes, but it is not a requirement.

- `type` MUST be set to `LNPubkey`
- `serviceEndpoint` MUST be set to a colon-deliminated string that first consist of `lightning` as the prefix and the 32 byte lightning public key of the 

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

```
{
"service": [
    {
      "id":"did:example:123#lnurl-pay",
      "type": "LNURL-Pay",
      "serviceEndpoint": "lightning:LNURL..."
    }
  ]
}
```

### Bolt12 Offers

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

TODO maybe put this at the beginning?

For each of the many service endpoint payment types, additional optional metadata can be added to specify preferences for the receiver.

#### Priority

For payment options, a proposal for how the recipient would prefer to get paid can be as simple as putting the parameter "priority": 1.

#### Min / Max

A minimum and maximum satoshi amount can be specified by putting the parameter "minAmountSat": 1 and "maxAmountSat": 100000. Msat & Btc could be supported as well instead of just Sat.


## DID Communication

### Lightning

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
      "priority": 1,
      "accept": [
         "didcomm/v2",
         "didcomm/aip2;env=rfc587"
       ]
    },
    {
      "id": "did:example:123#didcomm-2",
      "type": "DIDCommMessaging",
      "serviceEndpoint": "did:example:123#LNPubkey",
      "priority": 2,
      "minAmountSat": 100,
      "accept": [
         "didcomm/v2",
         "didcomm/aip2;env=rfc587"
       ]
    }
  ]
}
```

## Relays

TODO, was missing technical specs on this in the original document

### DIDComm Mediators

### Lightning Payment/Communication Mailboxes


## Supporting DID Ecosystem

`this is more of an additional explanations on how dids could function/resolve`

### Well Known DID

```
_did.example.net.  IN URI 100 10 "did:example:1234abcd"
```



## Examples

### Complete

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
