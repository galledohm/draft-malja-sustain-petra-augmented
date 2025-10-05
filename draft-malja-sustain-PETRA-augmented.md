---
title: "Path Energy Traffic Ratio API (PETRA) Augmented"
abbrev: "Energy-API Augmented"
category: info

docname: draft-malja-sustain-PETRA-augmented-latest
submissiontype: IRTF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date: 04/10/2025
consensus: true
v: 0.0
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
    city:
    country:
    email: jan.lindblad+ietf@for.eco
 -
    ins: A. Gallego Sanchez
    fullname: Adrian Gallego Sanchez
    organization: T-Systems
    city: Guadalajara
    country: Spain
    email: ADRIAN.GALLEGO-SANCHEZ@t-systems.com

normative:
  RFC2119:
  RFC3688:

informative:

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

TODO

# Security Considerations

TBD


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

Kudos to Elis Lulja for his help with the OpenAPI specification in early versions of this draft. Thanks to Fernando Sanz Garcia and Lori Jakab for their help and support on this work.

The contribution of Telefonica to this document has been supported by the Smart Networks and Services Joint Undertaking (SNS JU) under the European Union's Horizon Europe research and innovation projects 6Green (Grant Agreement no. 101096925) and Exigence (Grant Agreement no. 101139120).
