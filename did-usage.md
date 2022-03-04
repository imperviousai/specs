# Impervious DID Spec

`TODO expand on the technical primer`

How we utilize the DID spec for our applications. 


## Service Endpoint Examples

`TODO explain this`

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


## Bitcoin Service Endpoints

### Keysend / AMP

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
