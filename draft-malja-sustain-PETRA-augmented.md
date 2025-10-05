---
title: "Path Energy Traffic Ratio API (PETRA) Augmented"
abbrev: "Energy-API Augmented"
category: info

docname: draft-malja-sustain-PETRA-augmented-latest
submissiontype: IRTF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date: 04/10/2025
consensus: true
v: 3
area: "IRTF"
workgroup: "Proposed Sustainability and the Internet Proposed Research Group"
keyword:
 - energy
 - network
 - sustainability
 - API
venue:
  group: "Proposed Sustainability and the Internet Proposed Research Group"
  type: "Research Group"
  mail: "sustain@irtf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/sustain/"
  github: "galledohm/draft-malja-sustain-PETRA-augmented"
  latest: "https://galledohm.github.io/draft-malja-sustain-PETRA-augmented/draft-malja-sustain-PETRA-augmented.html"

author:
 -
    ins: A. Rodriguez-Natal
    fullname: Alberto Rodriguez-Natal
    organization: Cisco
    city: Madrid
    country: Spain
    email: natal@cisco.com
 -
    ins: L. M. Contreras
    fullname: Luis M. Contreras
    organization: Telefonica
    city: Madrid
    country: Spain
    email: luismiguel.contrerasmurillo@telefonica.com
 -
    ins: M. Palmero
    fullname: Marisol Palmero
    organization:
    city: Toledo
    country: Spain
    email: marisol.ietf@gmail.com
 -
    ins: J. Lindblad
    fullname: Jan Lindblad
    organization: All For Eco
    email: jan.lindblad+ietf@for.eco
 -
    ins: A. Gallego Sanchez
    fullname: Adrian Gallego Sanchez
    organization: T-Systems
    city: Guadalajara
    country: Spain
    email: ADRIAN.GALLEGO-SANCHEZ@t-systems.com

normative:
  RFC3688:
  RFC6020:
  RFC6241:
  RFC6242:
  RFC6991:
  RFC8040:
  RFC8309:
  RFC8340:

informative:
  I-D.belmq-green-framework:

...

--- abstract

This document describes an API to query a network regarding its Energy Traffic Ratio and other energy-related metric for a given network path.


--- middle

# Introduction

   Sustainability is becoming one of the major societal goals for the next decade, and networks are one of the major consumers of energy nowadays. Sustainability of network services is thus one of the forefronts of innovation and action from network service stakeholders, involving manufacturers, operators and customers. In this line, there is a shared goal of achieving better energy   awareness.

   As with any other network metric, the energy traffic ratio could be collected from the underlying network infrastructure.  However, there is not a common or single definition of energy metrics towards network consumers so that can be uniformly reported, particularly in  heterogeneous network scenarios. This document introduces an API to query networks about Energy Traffic Ratio.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Path Energy Traffic Ratio API (PETRA)

This documents describes an API to query a network about the Energy Traffic Ratio for a given path. It takes as input the source and destination of a path along with the traffic throughput between and returns energy information related to the traffic on the path. This is energy computed by the infrastructure that is dynamically part of the traffic path. The API is agnostic to the actual hops and underlaying infrastructure that enables a path, which might change transparently to the API. This document only describes the API, the computation of the energy information to return is out of the scope of this document.

## Energy Information

This API allows to return a number of energy attributes associated with the path and the traffic. Currently the parameters that could be returned as energy information as part of the query are:

- Watts per Gigabit: How many Watts are consumed per Gigabit of traffic traversing the path.
- Carbon Intensity: How much carbon emissions are generated as a consequence of the energy consumed.
Some other parameters that could be considered as well as part of the energy information include:

- Renewable Percentage: How much of the energy consumed comes from renewable energy sources.
- ...

## Recursive Usage

The API is envisioned in such a way that could be used recursively. That means, subpaths could report their energy consumption using PETRA and such energy consumption could be aggregated and reported for the overall path also using PETRA.

Similarly, this API could be (recursively) used to provide energy information according to the definition of Service Models in an SDN context as described in [RFC8309]. In that case, using Figure 3 in [RFC8309] as reference, PETRA could be used between the Controller(s) and the Network Orchestrator(s), between the Network Orchestrator(s) and the Service Orchestrator, and between the Service Orchestrator and the Customer(s).

While considering recursive usage, the aspect of double-counting shall also be taken into consideration. Double counting refers to the fact of counting more than once the same energy consumed. Organizations using PETRA in a recursive manner need to take appropriate measures to ensure no double-counting occurs across recursive calls to the API.

# YANG Module

This is a posible definition of PETRA as a module following the YANG specification [RFC6020].

## Module Structure

~~~~yang
module: ietf-petra
  +--rw energy
     +---x query
        +---w input
        |  +---w src-ip        ietf-inet-types:ip-address
        |  +---w dst-ip        ietf-inet-types:ip-address
        |  +---w throughput    uint32
        +--ro output
           +--ro (result)?
              +--:(success)
              |  +--ro success
              |     +--ro watts-per-gigabit?   decimal64
              |     +--ro carbon-intensity?    uint32
              +--:(invalid-address)
                 +--ro invalid-address
~~~~

## Module Definition

~~~~yang
module ietf-petra {
  yang-version 1.1;
  namespace "urn:ietf:params:xml:ns:yang:ietf-petra";
  prefix ietf-petra;

  import ietf-inet-types {
    prefix ietf-inet-types;
  }

  organization
    "";
  contact
    "";
  description
    "Initial YANG rendition of the PETRA Energy API, v1.0.1

    Copyright (c) 2025 IETF Trust and the persons identified as
    authors of the code. All rights reserved.

    Redistribution and use in source and binary forms, with or
    without modification, is permitted pursuant to, and subject to
    the license terms contained in, the Revised BSD License set
    forth in Section 4.c of the IETF Trust's Legal Provisions
    Relating to IETF Documents
    (https://trustee.ietf.org/license-info).

    This version of this YANG module is part of RFC XXXX
    (https://www.rfc-editor.org/info/rfcXXXX); see the RFC itself
    for full legal notices.

    The key words 'MUST', 'MUST NOT', 'REQUIRED', 'SHALL',
    'SHALL NOT', 'SHOULD', 'SHOULD NOT', 'RECOMMENDED',
    'NOT RECOMMENDED', 'MAY', and 'OPTIONAL' in this document
    are to be interpreted as described in BCP 14 (RFC 2119)
    (RFC 8174) when, and only when, they appear in all
    capitals, as shown here.
    ";

/*
    If you have an implementation of this YANG module, you could
    access it like something this over RESTCONF:

    $ curl --location --request POST \
      'https://localhost:8008/restconf/operations/energy/query' \
      --header 'Content-Type: application/yang-data+json' \
      --user 'admin:admin' \
      --data-raw '{
          'input' : {
              'src-ip': '10.10.10.10',
              'dst-ip': '10.20.20.20',
              'throughput': '40'
          }
        }'

    And if all goes well, you might receive (besides all the
    HTTP headers) a reply body with something like this:

    {
      'output': {
        'success': {
          'watts-per-gigabit': '191.855',
          'carbon-intensity': '108'
        }
      }
    }
*/

  revision 2025-05-12 {
    description
      "Initial YANG rendition of the PETRA Energy API, v1.0.1";
    reference
      "RFC XXXX: ...";
  }

  grouping energy-metrics-g {
    description
      "Grouping for query result metrics.";
    leaf watts-per-gigabit {
      type decimal64 {
        fraction-digits 3;
      }
      units W/Gb;
      description
        "Watts consumed per Gigabit transmitted";
    }
    leaf carbon-intensity {
      type uint32;
      units gCO2e/kWh;
      description
        "Grams of CO2 per kWh";
    }
  }

  container energy {
    description
      "PETRA API top level container.";
    action query {
      description
        "Query the network for energy consupmtion";
      input {
        leaf src-ip {
          type ietf-inet-types:ip-address;
          mandatory true;
          description
            "Source IP address";
        }
        leaf dst-ip {
          type ietf-inet-types:ip-address;
          mandatory true;
          description
            "Destination IP address";
        }
        leaf throughput {
          type uint32;
          units Gb/s;
          mandatory true;
          description
            "Throughput between source and destination
            (in gigabits per second)";
        }
      }
      output {
        choice result {
          description
            "Choice of which kind of result the query gave.";
          container success {
            description
              "Successful operation";
            uses energy-metrics-g;
          }
          container invalid-address {
            description
              "Invalid source/destination IP address supplied";
          }
        }
      }
    }
  }
}
~~~~

# Security Considerations

TBD


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

Kudos to Elis Lulja for his help with the OpenAPI specification in early versions of this draft. Thanks to Fernando Sanz Garcia and Lori Jakab for their help and support on this work.

The contribution of Telefonica to this document has been supported by the Smart Networks and Services Joint Undertaking (SNS JU) under the European Union's Horizon Europe research and innovation projects 6Green (Grant Agreement no. 101096925) and Exigence (Grant Agreement no. 101139120).

# Appendix A. Use Cases {#usecases}
{:numbered="false"}

This section describes some use-cases where this specification might be useful.

## A.1. SD-WAN
{:numbered="false"}

Software-Defined Wide-Area Networks (SD-WAN) have become a common way for enterprises to provide cost-effective connectivity across their different geographically distributed sites. Typically, SD-WAN deployments operate as an overlay network that is established on top of an existing underlay connectivity network. One aspect to consider is that in many SD-WAN production deployments the operator of the overlay network and the operator of the underlay network are different organizations.

This poses an additional challenge when trying to derive sustainability metrics. Even if the underlay network is instrumented to collect energy data, this data is opaque to the operator of the overlay network which has no access to underlay information. While operators of underlay networks offer certain general network metrics to overlay operators, no interface has been defined to allow the overlay operator to query the underlay network for energy information.

In this context, the PETRA specification presented in this document enables the operator of the SD-WAN network to coordinate with the underlay operator to capture sustainability data. This in turns opens further use-cases, from observability and reporting to potentially overlay policies based on underlay energy data, further enabling an overall more sustainable operation of the network.

In addition to energy considerations in SD-WAN deployments, PETRA can also be leveraged for broader energy-aware service routing. In this context, network controllers and service orchestrators—such as SD-WAN controllers, transport SDN controllers, 5G slice orchestrators, or multi-domain service orchestrators—can use PETRA metrics not only to balance latency, throughput, or load, but also to optimize path selection according to sustainability objectives. For example, paths with the lowest carbon intensity or the highest share of renewable energy in their energy mix could be preferred, enabling service differentiation where “green paths” are explicitly prioritized. This brings a paradigm where routing decisions are jointly driven by network performance and environmental impact.

## A.2. Multilayer Energy Management
{:numbered="false"}

The concept of multilayer L3-L1 collection involves integrating data from different network layers to provide a comprehensive view of network operations. The use case of multilayer involves collecting and correlating data from Layer 3 (network layer) down to Layer 1 (physical layer). This multilayer approach allows for better network performance, optimization, and troubleshooting by providing end-to-end visibility.

Leveraging PETRA API for multilayer L3-L1 collection use case enhances energy management by providing comprehensive visibility, enabling optimization, and supporting proactive management. This makes PETRA a useful tool for more accurate, efficient and effective energy management in modern networks.

## A.3. SLA Negotiation for Green Services
{:numbered="false"}

Another use case for PETRA could be the negotiation of green Service Level Agreements (gSLAs) between operators and enterprise customers. By exposing PETRA-derived metrics such as renewable energy percentage, carbon intensity, or sustainability scores, providers can offer differentiated SLAs that explicitly include environmental targets. This enables customers to select network services not only based on performance guarantees, but also on their environmental footprint, for example requesting that at least 60% of traffic be carried over renewable-powered infrastructure. Such gSLAs empower customers to align their digital services with corporate sustainability goals and reporting requirements, while operators can use PETRA as the trusted source of verifiable energy data.

# Appendix B. Requirements for Energy Efficiency Management
{:numbered="false"}

The document Framework for Energy Efficiency Management {{I-D.belmq-green-framework}} describes a framework that comprises a controller element. In that document, the tasks of the controller are defined as "collection, compute and aggregate". In the context of that framework, the controller could also expose PETRA to offer path-related energy information. The figure below updates the one present in {{I-D.belmq-green-framework}} to add an additional interface (interface 'g') to the controller to represent the Path Traffic Ratio API.

~~~~bash
+--------------------------------------------------------------------+
|                                                                    |
|                  (3) Network Domain Level                          |
|                                                                    |
+--------------------------------------------------------------------+

(a)             (b)             (c)
Inventory       Monitor      +- DataSheets/DataBase
Of identity     Energy       |  and/or via API
and Capability  Efficiency   |  Metadata and other
      ^              ^       | device/component/network
      |              |       | related information:                  (g)
      |              |       |                                      PETRA
      |              |       |  .Power/Energy related metrics         API
      |              |       |  .information                           ^
      |              |       |  .origin of Energy Mix                  |
      |              |       |  .carbon aware based on location        |
      |              |       |                                         |
      |              |       v                                         |
+--------------------------------------------------------------------+ |
|                                                                    | |
|       (2) controller (collection, compute and aggregate?)          +-+
|                                                                    |
+--------------------------------------------------------------------+
                ^                      ^                      ^ |
      (d)        |     (e)              |   (f)                | |
      Inventory  |     Monitor power    |   Control            | |
      Capability |     Proportion       |   (Energy saving     | |
                 |     Energy efficiency|   Functionality      | |
                 |     ratio, power     |   Localized mgmt/    | |
                 |     consumption,     |   network wide mgmt) | |
                 |     etc)             |                      | |
                 |                      |                      | v
+--------------------------------------------------------------------+
|                                                                    |
|                       (1) Device/Component                         |
|                                                                    |
| +---------+  +-----------+  +----------------+  +----------------+ |
| | (I)     |  | (II)      |  | (III)          |  | (IV)           | |
| |         |  |           |  | Legacy         |  | 'Attached'(PoE | |
| | Device  |  | Component |  | Device         |  | end Point)     | |
| |         |  |           |  |                |  |                | |
| +---------+  +-----------+  +----------------+  +----------------+ |
+--------------------------------------------------------------------+
~~~~
{: #petra-framework title="PETRA Integration in Energy Management Framework"}
