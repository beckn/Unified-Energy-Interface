# EV Charging and Battery Swapping Application Workflow #1

## Overview

This document outlines the workflow for Electric Vehicle (EV) Charging and Battery Swapping using the DENT Protocol. The workflow includes interactions between the provider and the User for search, select, block, and completing the session.

Bear in mind that this is just an example workflow for a simple EV Charing and Battery swap workflow between a `User` and a `Provider`.
(Note: Here, User -> Electric Vehicle Owner/User and Provider -> EV Chraging Provider or Battery Swapping Service Provider)

A typical workflow for EV Charging & Battery Swapping consists of the following steps:

#### Step 1: User searches for EV Chargers

The user provides current location and specific requirements (These requirements will act as filters for EV Charging Providers such as amount of energy user needs in kwh) to find the nearest EV Chargers to charge his/her EV

#### Step 2: Provider sends catalogs of EV Chargers nearby

The provider platform (BPP) sends all the nearby Providers and their catalogs to the user.
The catalog will consist of all the services provided by the providers, such as charging types, connector types and battery swapping services etc.,

#### Step 3: User selects EV Charger or Battery Swapping Service

Selects the provider which satisfies user requirements.
In this stage, the user may select additional features such as:

1. User can select his required services (such as EV Charging or Battery Swapping) from the catalog of provider
2. User selects the vehicle type of his EV (2-wheeler/3-wheeler/4-wheeler), selects the battery type (such as log9 battery), and also provides the EV's information.
3. User can block/reserve the charger/swap service for a certain time slot, along with the start and duration times of the session.
4. User can select the charger type he wants to use such as A.C. or D.C. and Connector types like CCS2 etc.,
5. User can select quantity of energy required in units of kwh.

#### Step 4: Provider sends quoted price

The provider will receive the order based on the user requirements.
The user gets the quoted price, including the breakdown of the price details.
The breakdown should include:

- Tariff per unit (i.e., INR/KWh), the tariff per unit might change by the service type, charger type and location of Energy Charger Provider
- (or) Price for Battery Swapping
- Price for reservation
- Taxes

#### Step 5: User initiates the order

User initializes the order by providing billing details.
Here, the user will provide their `Name`, `Email ID` and `Phone Number` and updates the payment details

#### Step 6: Provider sends draft order

The provider sends the draft order with the payment and fulfilment terms to the user-side.
Based on how much units of Electrical Energy user used during charging as tariff_per_unit as already mentioned above in INR/KWh or the Price for Battery Swapping Service
If the user also done the reservation/block the transaction includes the price for block/use-up price as well
The tariff_per_unit might change with the charger type as we know that A.C. charger type has different price compared to D.C. charger type.

#### Step 7: User confirms the order

User sees the draft order and confirms it by agreeing to the payment and fulfilment terms and conditions
The confirm status will sent to the provider saying that user has paid the price and satisfied the fulfillment terms

#### Step 8: Provider sends the order activation

The provider will activates the order and informs user that the order is activated

#### Step 9: User checks the status of the order

The user requests to check the updates/status of his/her order

#### Step 10: Provider sends the status of the order

The provider will send the order updates with current status to the user

## Search (Searching for EV Chargers)

1. The user declares the intent for EV Charging/Battery Swap to the providers
2. Providers publish the catalog of their services

### User-side Actions

A EV user can declare their intent for charging their EV in many ways like:

- Searching for EV Charging/Battery Swap Providers based on current location of user
- Searching for EV Charging Providers based on quantity required
- Searching for Battery Swap Providers based on battery type
- Searching for EV Charging/Battery Swap Providers based on Name/Code of provider
- Searching for EV Charging/Battery Swap Providers based on ratings
- View list of energy sources, such as EV chargers, Solar Farms providing EV charging, Houses providing EV charging, Battery Swap Providers etc.
- Viewing the catalog/details of services provided by a particular charging/Battery Swap provider

### Provider-side Actions

In this interaction, the Provider publishes their catalog of services and products. A Provider can publish various types of catalogs like

- Publish list of energy sources and EV chargers including Solar Farms providing EV charging, Houses, Battery Swap Providers etc.
- Publish list of provders in 5Km radius of user
- Publish list of provders with the requested Charing types/Connector types/Battery types
- Publish catalog/details of services provided by providers
- Publish catalog of services provided by a particular provider

### Logical Workflow

```mermaid
    sequenceDiagram
    User->>Provider: Declare Intent to EV Charging / Battery swapping
    Provider->>User: Publish Catalog of services available
```

### Beckn Protocol API Workflow

In beckn protocol, the search intent generated by the EV User Platform (BAP) is typically published on the gateway (BG) that broadcasts the intent to multiple Provider platforms (BPPs). Each of the BPPs return their catalogs directly to the BAP via asynchronous callbacks. The workflow for that is shown below.

```mermaid
    sequenceDiagram
    User Platform (BAP)->>Gateway (BG): Declare Intent to charge EV / swap battery ( search )
    Gateway (BG)->>Registry: Lookup Provider Platforms (lookup)
    Registry->>Gateway (BG): List of Provider Platforms in 'x'km radius and 'y'kwh energy quantity required (200 OK)
    Gateway (BG)->>Provider Platform 1 (BPP1): Declare Intent to charge EV or Swap Battery (search)
    Gateway (BG)->>Provider Platform 2 (BPP2): Declare Intent to charge EV or Swap Battery (search)
    Gateway (BG)->>Provider Platform n (BPPn): Declare Intent to charge EV or Swap Battery(search)
    Provider Platform 1 (BPP1)->>User Platform (BAP): Publish Catalog of Provider 1 (on_search)
    Provider Platform 2 (BPP2)->>User Platform (BAP): Publish Catalog of Provider 2 (on_search)
    Provider Platform n (BPPn)->>User Platform (BAP): Publish Catalog of Provider n (on_search)
```

#### Example `search` api Json:

```json
{
  "context": {
    "domain": "dent:0.1.0",
    "action": "search",
    "location": {
      "country": {
        "name": "India",
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
        },
        "category": {
          "descriptor": {
            "code": "green-tariff"
          }
        }
      },
      "location": {
        "gps": "12.423423,77.325647",
        "radius": {
          "type": "CONSTANT",
          "value": "5",
          "unit": "km"
        }
      }
    }
  }
}
```

#### Example `on_search` api json:

```json
{
  "context": {
    "domain": "dent:0.1.0",
    "action": "on_search",
    "location": {
      "country": {
        "name": "India",
        "code": "IND"
      }
    },
    "city": "std:080",
    "version": "1.1.0",
    "bap_id": "example-bap.com",
    "bap_uri": "https://api.example-bap.com/pilot/bap/energy/v1",
    "bpp_id": "example-bpp.com",
    "bpp_uri": "https://api.example-bpp.com/pilot/bpp/",
    "transaction_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
    "message_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
    "timestamp": "2023-07-16T04:41:16Z"
  },
  "message": {
    "catalog": {
      "providers": [
        {
          "id": "chargezone.in",
          "descriptor": {
            "name": "Chargezone",
            "short_desc": "Chargezone Technologies Pvt Ltd",
            "images": [
              {
                "url": "https://chargezone.in/images/logo.png"
              }
            ]
          },
          "locations": [
            {
              "id": "1",
              "gps": "12.345345,77.389754"
            },
            {
              "id": "2",
              "gps": "12.247934,77.876987"
            }
          ],
          "items": [
            {
              "id": "pe-charging-01",
              "descriptor": {
                "code": "energy"
              },
              "price": {
                "value": "8",
                "currency": "INR / kWH"
              },
              "quantity": {
                "available": {
                  "measure": {
                    "value": "100",
                    "unit": "kWH"
                  }
                }
              },
              "locations": ["1", "2"],
              "fulfillments": ["1", "2"]
            }
          ],
          "fulfillments": [
            {
              "id": "1",
              "type": "CHARGING",
              "stops": [
                {
                  "time": {
                    "range": {
                      "start": "10:00",
                      "end": "10:30"
                    }
                  }
                }
              ],
              "tags": [
                {
                  "descriptor": {
                    "name": "Charging Point Specifications"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "Charger type",
                        "code": "charger-type"
                      },
                      "value": "AC"
                    },
                    {
                      "descriptor": {
                        "name": "Connector type",
                        "code": "connector-type"
                      },
                      "value": "CCS2"
                    },
                    {
                      "descriptor": {
                        "name": "Power Rating"
                      },
                      "value": "greater than 50kW"
                    },
                    {
                      "descriptor": {
                        "name": "Availability"
                      },
                      "value": "Available"
                    }
                  ],
                  "display": true
                }
              ]
            },
            {
              "id": "2",
              "type": "CHARGING",
              "stops": [
                {
                  "time": {
                    "range": {
                      "start": "10:30",
                      "end": "11:00"
                    }
                  }
                }
              ],
              "tags": [
                {
                  "descriptor": {
                    "name": "Charging Point"
                  },
                  "list": [
                    {
                      "descriptor": {
                        "name": "Charger type"
                      },
                      "value": "AC"
                    },
                    {
                      "descriptor": {
                        "name": "Connector type"
                      },
                      "value": "CCS2"
                    },
                    {
                      "descriptor": {
                        "name": "Power Rating"
                      },
                      "value": "greater than 50kW"
                    },
                    {
                      "descriptor": {
                        "name": "Availability"
                      },
                      "value": "Available"
                    }
                  ],
                  "display": true
                }
              ]
            }
          ]
        },
        {
          "id": "log9.in",
          "descriptor": {
            "name": "Log9 Inc"
          },
          "items": [
            {
              "id": "pe-charging-01",
              "descriptor": {
                "code": "energy"
              },
              "price": {
                "value": "10",
                "currency": "INR / kWH"
              },
              "quantity": {
                "available": "1000"
              },
              "fulfillments": ["3", "4"]
            }
          ],
          "fulfillments": [
            {
              "id": "3",
              "type": "BATTERY-SWAP",
              "stops": [
                {
                  "location": {
                    "gps": "12.745675, 77.987393"
                  }
                }
              ]
            },
            {
              "id": "4",
              "type": "MOBILE-BATTERY-SWAP",
              "stops": [
                {
                  "location": {
                    "url": "https://pulseenergy.in/track/bswap/3234242"
                  }
                }
              ]
            }
          ]
        }
      ]
    }
  }
}
```

## Select and Book Charging

1. User selects a EV Charge/ Battery Swap Provider from the list which satisfies the requirements
2. The provider sends the draft order with the quoted price to the User

### User-side Actions

- Selecting EV Charge/Battery Swap Provider(s) after viewing catalogs
- Selecting to block/use-up charger (or) not. (Y/N)
- Selecting start and end time for block/use-up of charger/swap service
- Selecting charger type (A.C./D.C.) and connector type
- Selecting vehicle type (2-wheeler, 3-wheeler, 4-wheeler) and its battery type

### Provider-side Actions

- Receive user's selection
- Requesting for block/use-up charger (Y/N)
- Requesting for time slot
- Requesting for charger type, connecter type, vehicle type and battery type
- Provider sends draft order for the selected EV charge with quoted price
- The price includes with a breakdown of `tariff_per_unit` (or) `price for battery swap`, `Reservation Price`, `taxes`

### Logical Workflow

The below diagram illustrates the logical interactions between a EV user and Provider during the Selecting service/product stage

```mermaid
    sequenceDiagram
    User->>Provider: Selects a EV charger/battery swap provider
    Provider->>User: Request for block/use-up (Y/N)
    User->>Provider: Selects for block/use-up (Y/N)
    Provider->>User: Request for time slot - start & end
    User->>Provider: Selects start & end time slot
    Provider->>User: Requesting for charger type, vehicle type & battery type
    User->>Provider: Selects charger, vehicle and battery type
    Provider->>User: Sends the draft order with quoted price
```

### Beckn Protocol API Workflow

```mermaid
   sequenceDiagram
   User Platform (BAP)->> Provider 1 (BPP): Select EV charge/Battery swap Provider after seeing catalogs of all providers (select)
   Provider 1 (BPP)->>User Platform (BAP): Publish Provider 1 catalog (on_select)
   User Platform (BAP)->> Provider 1 (BPP): Select one from EV charging & Battery Swap as a service from Provider 1 (select)
   Provider 1 (BPP)->>User Platform (BAP): Request block/use-up (on_select)
   User Platform (BAP)->> Provider 1 (BPP): Select block/use-up (select)
   Provider 1 (BPP)->>User Platform (BAP): Request time slot to block, charging type, vehicle type and battery type of EV (on_select)
   User Platform (BAP)->> Provider 1 (BPP): Select time slot to block and charging type, vehicle type and battery type of EV (select)
   Provider 1 (BPP)->>User Platform (BAP): Send draft order with quoted price and breakdown (on_select)
```

#### Example `select` api json:

```json
{
  "context": {
    "domain": "dent:0.1.0",
    "action": "select",
    "location": {
      "country": {
        "name": "India",
        "code": "IND"
      }
    },
    "city": "std:080",
    "version": "1.1.0",
    "bap_id": "example-bap.com",
    "bap_uri": "https://api.example-bap.com/pilot/bap/energy/v1",
    "bpp_id": "pulse-energy-bpp.com",
    "bpp_uri": "https://api.pulse-energy-bpp.com/pilot/bap/energy/v1",
    "transaction_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
    "message_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
    "timestamp": "2023-07-16T04:41:16Z"
  },
  "message": {
    "order": {
      "provider": {
        "id": "chargezone.in"
      },
      "items": [
        {
          "id": "pe-charging-01",
          "quantity": {
            "selected": {
              "measure": {
                "value": "4",
                "unit": "kWh"
              }
            }
          }
        }
      ]
    }
  }
}
```

#### Example `on_select` api json:

```json
{
  "context": {
    "domain": "dent:0.1.0",
    "action": "on_select",
    "location": {
      "country": {
        "name": "India",
        "code": "IND"
      }
    },
    "city": "std:080",
    "version": "1.1.0",
    "bap_id": "example-bap.com",
    "bap_uri": "https://api.example-bap.com/pilot/bap/energy/v1",
    "bpp_id": "pulse-energy-bpp.com",
    "bpp_uri": "https://api.pulse-energy-bpp.com/pilot/bap/energy/v1",
    "transaction_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
    "message_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
    "timestamp": "2023-07-16T04:41:16Z"
  },
  "message": {
    "order": {
      "providers": {
        "id": "chargezone.in",
        "descriptor": {
          "name": "Chargezone",
          "short_desc": "Chargezone Technologies Pvt Ltd",
          "images": [
            {
              "url": "https://chargezone.in/images/logo.png"
            }
          ]
        }
      },
      "items": [
        {
          "id": "pe-charging-01",
          "descriptor": {
            "code": "energy"
          },
          "price": {
            "value": "8",
            "currency": "INR/kWH"
          },
          "quantity": {
            "available": {
              "measure": {
                "value": "100",
                "unit": "kWh"
              }
            },
            "selected": {
              "measure": {
                "value": "4",
                "unit": "kWh"
              }
            }
          }
        }
      ],
      "quote": {
        "price": {
          "value": "32",
          "currency": "INR"
        },
        "breakup": [
          {
            "item": {
              "descriptor": {
                "name": "Estimated units consumed"
              },
              "quantity": {
                "selected": {
                  "measure": {
                    "value": "4",
                    "unit": "kWh"
                  }
                }
              }
            },
            "price": {
              "value": "32",
              "currency": "INR"
            }
          }
        ]
      }
    }
  }
}
```

## Order Initialization

In this stage, the User provides the required information and initiates the order

### User-side Actions

- User provides the billing details `Name`, `Email ID` and `Phone Number`
- User updates the payment details and initiates the order

### Provider-side Actions

- Request for billing details
- Receive billing details from the user
- Send draft order with payment and fulfillment terms

### Logical Workflow

```mermaid
    sequenceDiagram
    User->>Provider: Provides billing details and initates the order
    Provider->>User: Send draft order with payment transcript and fulfillment terms
```

### Beckn Protocol API Workflow

```mermaid
    sequenceDiagram
    User Platform (BAP)->> Provider 1 (BPP): BAP updates billing details and initiates the order(init)
    Provider 1 (BPP)->>User Platform (BAP): BPP sends draft order with breakdown and fulfillment terms (on_init)
```

#### Example `init` api json:

```json
{
  "context": {
    "domain": "dent:0.1.0",
    "action": "init",
    "location": {
      "country": {
        "name": "India",
        "code": "IND"
      }
    },
    "city": "std:080",
    "version": "1.1.0",
    "bap_id": "example-bap.com",
    "bap_uri": "https://api.example-bap.com/pilot/bap/energy/v1",
    "bpp_id": "pulse-energy-bpp.com",
    "bpp_uri": "https://api.pulse-energy-bpp.com/pilot/bap/energy/v1",
    "transaction_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
    "message_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
    "timestamp": "2023-07-16T04:41:16Z"
  },
  "message": {
    "order": {
      "provider": {
        "id": "chargezone.in"
      },
      "items": [
        {
          "id": "pe-charging-01"
        }
      ],
      "billing": {
        "name": "John Doe",
        "email": "abc@example.com",
        "phone": "+91-9876522222"
      },
      "fulfillments": [
        {
          "id": "1",
          "customer": {
            "person": {
              "name": "John Doe"
            },
            "contact": {
              "phone": "+91-9887766554"
            }
          }
        }
      ]
    }
  }
}
```

#### Example `on_init` api json:

```json
{
  "context": {
    "domain": "dent:0.1.0",
    "action": "on_init",
    "location": {
      "country": {
        "name": "India",
        "code": "IND"
      }
    },
    "city": "std:080",
    "version": "1.1.0",
    "bap_id": "example-bap.com",
    "bap_uri": "https://api.example-bap.com/pilot/bap/energy/v1",
    "bpp_id": "pulse-energy-bpp.com",
    "bpp_uri": "https://api.pulse-energy-bpp.com/pilot/bap/energy/v1",
    "transaction_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
    "message_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
    "timestamp": "2023-07-16T04:41:16Z"
  },
  "message": {
    "order": {
      "providers": {
        "id": "pulse-energy",
        "descriptor": {
          "name": "Pulse Energy",
          "short_desc": "Pulse Energy Technologies Pvt Ltd",
          "images": [
            {
              "url": "https://pulseenergy.io/images/logo.png"
            }
          ]
        }
      },
      "items": [
        {
          "id": "pe-charging-01",
          "descriptor": {
            "code": "ee"
          },
          "price": {
            "value": "8",
            "currency": "INR/kWH"
          },
          "quantity": {
            "available": "100"
          },
          "fulfillments": ["1"]
        }
      ],
      "fulfillments": [
        {
          "id": "1",
          "type": "CHARGING",
          "state": {
            "descriptor": {
              "code": "order-initiated"
            }
          },
          "vehicle": {
            "category": "car",
            "make": "Tata",
            "model": "Nexon EV",
            "variant": "ZX+",
            "wheels_count": "4",
            "energy_type": "electric"
          },
          "stops": [
            {
              "time": {
                "range": {
                  "start": "10:00",
                  "end": "10:30"
                }
              }
            }
          ],
          "tags": [
            {
              "descriptor": {
                "name": "Charging Point"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "Charger type"
                  },
                  "value": "AC"
                },
                {
                  "descriptor": {
                    "name": "Connector type"
                  },
                  "value": "CCS2"
                },
                {
                  "descriptor": {
                    "name": "Power Rating"
                  },
                  "value": "greater than 50kW"
                },
                {
                  "descriptor": {
                    "name": "Availability"
                  },
                  "value": "Available"
                }
              ],
              "display": true
            }
          ]
        }
      ],
      "billing": {
        "email": "abc@example.com",
        "number": "+91-9876522222"
      },
      "quote": {
        "price": {
          "value": "40",
          "currency": "INR"
        },
        "breakup": [
          {
            "title": "units consumed",
            "price": { "value": "40", "currency": "INR" }
          },
          {
            "title": "charging fees",
            "price": { "value": "10", "currency": "INR" }
          },
          {
            "title": "cgst",
            "price": { "value": "4", "currency": "INR" }
          },
          {
            "title": "sgst",
            "price": { "value": "4", "currency": "INR" }
          }
        ]
      },
      "payments": [
        {
          "url": "https://payment.gateway.in",
          "type": "PRE-ORDER",
          "status": "NOT-PAID",
          "params": {
            "amount": "40",
            "currency": "INR"
          },
          "time": {
            "range": {
              "start": "2023-08-10T10:00:00Z",
              "end": "2023-08-10T10:30:00Z"
            }
          }
        }
      ]
    }
  }
}
```

## Fulfillment (Payment and Order Confirmation)

User will check the order details and confirms the order with payment (might also update the order)
Post payment user will activates the confirmed order

### User-side Actions

- Confirms(\updates) the order by agreeing to fulfillment terms

### Provider-side Actions

- Receive order confirmation from the user
- Send active confirmed order to the user

## Status Updates and Monitoring

### User-side Actions

- Request to fetch the latest status of the order

#### Provider-side Actions

- Provide the latest status of the order to the user

### Logical Worklow

```mermaid
    sequenceDiagram
    User->>Provider: Confirms (\Updates) the order and pays the the price
    Provider->>User: Send active confirmed order
    User->>Provider: Request for latest status of order
    Provider->>User: Sends the latest status of order
```

### Beckn API Workflow

```mermaid
    sequenceDiagram
    User Platform (BAP)->> Provider 1 (BPP): BAP updates the payment and confirms the order and fulfillment terms(confirm)
    Provider 1 (BPP)->>User Platform (BAP): BPP sends active confirmation of order (on_confirm)
    User Platform (BAP)->> Provider 1 (BPP): BAP request to fetch the status of the order (status)
    Provider 1 (BPP)->>User Platform (BAP): BPP provides the latest status of the order to BAP (on_status)
```

#### Example `confirm` api json:

```json
{
  "context": {
    "domain": "dent:0.1.0",
    "action": "confirm",
    "location": {
      "country": {
        "name": "India",
        "code": "IND"
      }
    },
    "city": "std:080",
    "version": "1.1.0",
    "bap_id": "example-bap.com",
    "bap_uri": "https://api.example-bap.com/pilot/bap/energy/v1",
    "bpp_id": "pulse-energy-bpp.com",
    "bpp_uri": "https://api.pulse-energy-bpp.com/pilot/bap/energy/v1",
    "transaction_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
    "message_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
    "timestamp": "2023-07-16T04:41:16Z"
  },
  "message": {
    "order": {
      "providers": {
        "id": "pulse-energy"
      },
      "items": [
        {
          "id": "pe-charging-01"
        }
      ],
      "billing": {
        "email": "abc@example.com",
        "number": "+91-9876522222"
      },
      "fulfillments": [
        {
          "id": "1"
        },
        {
          "vehicle": {
            "category": "car",
            "make": "Tata",
            "model": "Nexon EV",
            "variant": "ZX+",
            "wheels_count": "4",
            "energy_type": "electric"
          }
        }
      ]
    }
  }
}
```

#### Example `on_confirm` api json:

```json
{
  "context": {
    "domain": "dent:0.1.0",
    "action": "on_confirm",
    "location": {
      "country": {
        "name": "India",
        "code": "IND"
      }
    },
    "city": "std:080",
    "version": "1.1.0",
    "bap_id": "example-bap.com",
    "bap_uri": "https://api.example-bap.com/pilot/bap/energy/v1",
    "bpp_id": "pulse-energy-bpp.com",
    "bpp_uri": "https://api.pulse-energy-bpp.com/pilot/bap/energy/v1",
    "transaction_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
    "message_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
    "timestamp": "2023-07-16T04:41:16Z"
  },
  "message": {
    "order": {
      "id": "6743e9e2-4fb5-487c-92b7",
      "providers": {
        "id": "pulse-energy",
        "descriptor": {
          "name": "Pulse Energy",
          "short_desc": "Pulse Energy Technologies Pvt Ltd",
          "images": [
            {
              "url": "https://pulseenergy.io/images/logo.png"
            }
          ]
        }
      },
      "items": [
        {
          "id": "pe-charging-01",
          "descriptor": {
            "code": "ee"
          },
          "price": {
            "value": "8",
            "currency": "INR/kWH"
          },
          "quantity": {
            "available": "100"
          },
          "fulfillments": ["1"]
        }
      ],
      "fulfillments": [
        {
          "id": "1",
          "type": "CHARGING",
          "state": {
            "descriptor": {
              "code": "payment-completed"
            }
          },
          "vehicle": {
            "category": "car",
            "make": "Tata",
            "model": "Nexon EV",
            "variant": "ZX+",
            "wheels_count": "4",
            "energy_type": "electric"
          },
          "stops": [
            {
              "time": {
                "range": {
                  "start": "10:00",
                  "end": "10:30"
                }
              }
            }
          ],
          "tags": [
            {
              "descriptor": {
                "name": "Charging Point"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "Charger type"
                  },
                  "value": "AC"
                },
                {
                  "descriptor": {
                    "name": "Connector type"
                  },
                  "value": "CCS2"
                },
                {
                  "descriptor": {
                    "name": "Power Rating"
                  },
                  "value": "greater than 50kW"
                },
                {
                  "descriptor": {
                    "name": "Availability"
                  },
                  "value": "Available"
                }
              ],
              "display": true
            }
          ]
        }
      ],
      "billing": {
        "email": "abc@example.com",
        "number": "+91-9876522222"
      },
      "quote": {
        "price": {
          "value": "40",
          "currency": "INR"
        },
        "breakup": [
          {
            "title": "charging cost",
            "price": { "value": "32", "currency": "INR" }
          },
          {
            "title": "cgst",
            "price": { "value": "4", "currency": "INR" }
          },
          {
            "title": "sgst",
            "price": { "value": "4", "currency": "INR" }
          }
        ]
      },
      "payments": [
        {
          "type": "PRE-FULFILLMENT",
          "status": "PAID",
          "params": {
            "amount": "40",
            "currency": "INR"
          },
          "time": {
            "range": {
              "start": "2023-08-10T10:00:00Z",
              "end": "2023-08-10T10:30:00Z"
            }
          }
        }
      ]
    }
  }
}
```

#### #### Example `status` api json:

```json
{
  "context": {
    "domain": "dent:0.1.0",
    "action": "status",
    "location": {
      "country": {
        "name": "India",
        "code": "IND"
      }
    },
    "city": "std:080",
    "version": "1.1.0",
    "bap_id": "example-bap.com",
    "bap_uri": "https://api.example-bap.com/pilot/bap/energy/v1",
    "bpp_id": "pulse-energy-bpp.com",
    "bpp_uri": "https://api.pulse-energy-bpp.com/pilot/bap/energy/v1",
    "transaction_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
    "message_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
    "timestamp": "2023-07-16T04:41:16Z"
  },
  "message": {
    "order_id": "6743e9e2-4fb5-487c-92b7"
  }
}
```

#### #### Example `status` api json:

```json
{
  "context": {
    "domain": "dent:0.1.0",
    "action": "on_status",
    "location": {
      "country": {
        "name": "India",
        "code": "IND"
      }
    },
    "city": "std:080",
    "version": "1.1.0",
    "bap_id": "example-bap.com",
    "bap_uri": "https://api.example-bap.com/pilot/bap/energy/v1",
    "bpp_id": "pulse-energy-bpp.com",
    "bpp_uri": "https://api.pulse-energy-bpp.com/pilot/bap/energy/v1",
    "transaction_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
    "message_id": "6743e9e2-4fb5-487c-92b7-13ba8018f176",
    "timestamp": "2023-07-16T04:41:16Z"
  },
  "message": {
    "order": {
      "id": "6743e9e2-4fb5-487c-92b7",
      "providers": {
        "id": "pulse-energy",
        "descriptor": {
          "name": "Pulse Energy",
          "short_desc": "Pulse Energy Technologies Pvt Ltd",
          "images": [
            {
              "url": "https://pulseenergy.io/images/logo.png"
            }
          ]
        }
      },
      "items": [
        {
          "id": "pe-charging-01",
          "descriptor": {
            "code": "ee"
          },
          "price": {
            "value": "8",
            "currency": "INR/kWH"
          },
          "quantity": {
            "available": "100"
          },
          "fulfillments": ["1"]
        }
      ],
      "fulfillments": [
        {
          "id": "1",
          "type": "CHARGING",
          "state": {
            "descriptor": {
              "name": "vehicle 65% charged"
            }
          },
          "vehicle": {
            "category": "car",
            "make": "Tata",
            "model": "Nexon EV",
            "variant": "ZX+",
            "wheels_count": "4",
            "energy_type": "electric"
          },
          "stops": [
            {
              "time": {
                "range": {
                  "start": "10:00",
                  "end": "10:30"
                }
              }
            }
          ],
          "tags": [
            {
              "descriptor": {
                "name": "Charging Point"
              },
              "list": [
                {
                  "descriptor": {
                    "name": "Charger type"
                  },
                  "value": "AC"
                },
                {
                  "descriptor": {
                    "name": "Connector type"
                  },
                  "value": "CCS2"
                },
                {
                  "descriptor": {
                    "name": "Power Rating"
                  },
                  "value": "greater than 50kW"
                },
                {
                  "descriptor": {
                    "name": "Availability"
                  },
                  "value": "Available"
                }
              ],
              "display": true
            }
          ]
        }
      ],
      "billing": {
        "email": "abc@example.com",
        "number": "+91-9876522222"
      },
      "quote": {
        "price": {
          "value": "40",
          "currency": "INR"
        },
        "breakup": [
          {
            "title": "charging cost",
            "price": { "value": "32", "currency": "INR" }
          },
          {
            "title": "cgst",
            "price": { "value": "4", "currency": "INR" }
          },
          {
            "title": "sgst",
            "price": { "value": "4", "currency": "INR" }
          }
        ]
      },
      "payments": [
        {
          "type": "PRE-FULFILLMENT",
          "status": "PAID",
          "params": {
            "amount": "40",
            "currency": "INR"
          },
          "time": {
            "range": {
              "start": "2023-08-10T10:00:00Z",
              "end": "2023-08-10T10:30:00Z"
            }
          }
        }
      ]
    }
  }
}
```
