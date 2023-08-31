# Implementation Guide
This document contains the REQUIRED and RECOMMENDED standard functionality that must be implemented by any Energy Producer Platform a.k.a BPPs and Energy Consumer Platforms a.k.a BAPs.

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC2119](https://datatracker.ietf.org/doc/html/rfc2119) from IETF.

## 5.1 Discovery of Energy Sources

### 5.1.1 Recommendations for BPPs
The following recommendations need to be considered when implementing discovery functionality for an Energy Provider BPP

- REQUIRED. The BPP MUST implement the `search` endpoint to receive an `Intent` object sent by BAPs
- REQUIRED. The BPP MUST return a catalog of energy sources on the `on_search` callback endpoint specified in the `context.bpp_uri` field of the `search` request body.
- REQUIRED. The BPP MUST map its energy source to the `Item` schema.
- REQUIRED. Any energy provider-related information like name, logo, short description must be mapped to the `Provider.descriptor` schema
- REQUIRED. If the BPP wants to group its energy sources under a specific category, it must map each category to the `Category` schema
- REQUIRED. Any energy transfer related information MUST be mapped to the `Fulfillment` schema.
- REQUIRED. If the BPP does not want to respond to a search request, it MUST return a `ack.status` value equal to `NACK`
- RECOMMENDED. Upon receiving a `search` request, the BPP SHOULD return a catalog that best matches the intent. This can be done by indexing the catalog against the various probable paths in the `Intent` schema relevant to typical financial service use cases

### 5.1.2 Recommendations for BAPs
- REQUIRED. The BAP MUST call the `search` endpoint of the BG to discover multiple BPPs on a network
- REQUIRED. The BAP MUST implement the `on_search` endpoint to consume the `Catalog` objects containing Energy Sources sent by BPPs.
- REQUIRED. The BAP MUST expect multiple catalogs sent by the respective Energy Providers on the network
- REQUIRED. The energy sources can be found in the `Catalog.providers[].items[]` array in the `on_search` request
- REQUIRED. If the `catalog.providers[].items[].xinput` object is present, then the BAP MUST redirect the user to, or natively render the form present on the link specified on the `items[].xinput.form.url` field.
- REQUIRED. If the `catalog.providers[].items[].xinput.required` field is set to `"true"` , then the BAP MUST NOT fire a `select`, `init` or `confirm` call until the form is submitted and a successful response is received
- RECOMMENDED. If the `catalog.providers[].items[].xinput.required` field is set to `"false"` , then the BAP SHOULD allow the user to skip filling the form


### Example
The search is broadcast to all providers on the network, there will be many providers. The providers could be EV chargers, Discoms or Energy Aggregators. The search request can look something like this.
- have to be mapped to the provider schema (then put on_search)

```
{
    "context": {
        "domain": "dent:0.1.0",
        "action": "search",
        "location": {
            "country": {
                "code": "IND"
            }
        },
        "city": "std:080",
        "version": "1.1.0",
        "bap_id": "example-bap.com",
        "bap_uri": "https://api.example-bap.com/pilot/bap/energy/v1",
        "transaction_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
        "message_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
        "timestamp": "2023-07-16T04:41:16Z"
    },
    "message": {
        "intent": {
            "item": {
                "descriptor": {
                    "code": "energy"
                },
                "quantity": {
                    "required": {
                        "value": "4.0",
                        "unit": "kWH"
                    }
                }
            },
            "location": {
                "gps": "12.423423,77.325647",
                "radius": {
                    "value": "5",
                    "unit": "km"
                }
            }
        }
    }
}
```

## Ordering

## Fulfillment

## Post-fulfillment
