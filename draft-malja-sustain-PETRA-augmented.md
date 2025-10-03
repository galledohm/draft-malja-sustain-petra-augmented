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
area: IRTF
workgroup: SUSTAIN
keyword:
 - energy
 - network
 - sustainability
 - API
venue:
  group: RG
  type: Research Group
  mail: sustain@irtf.org
  arch: https://www.irtf.org/sustain.html
  github: USER/REPO
  latest: https://example.com/LATEST

author:
 -
    ins: A. Rodriguez-Natal
    fullname: Alberto Rodriguez-Natal
    organization: Cisco
    email: natal@cisco.com
    ins: L. M. Contreras
    fullname: Luis M. Contreras
    organization: Telefonica
    email: luismiguel.contrerasmurillo@telefonica.com
    ins: M. Palmero
    fullname: Marisol Palmero
    organization: 
    email: marisol.ietf@gmail.com
    ins: J. Lindblad
    fullname: Jan Lindblad
    organization: All For Eco
    email: jan.lindblad+ietf@for.eco
    ins: A. Gallego Sanchez
    fullname: Adrian Gallego Sanchez
    organization: T-Systems
    email: ADRIAN.GALLEGO-SANCHEZ@t-systems.com

normative:

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


# Security Considerations

TBD


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

Kudos to Elis Lulja for his help with the OpenAPI specification in early versions of this draft. Thanks to Fernando Sanz Garcia and Lori Jakab for their help and support on this work.

The contribution of Telefonica to this document has been supported by the Smart Networks and Services Joint Undertaking (SNS JU) under the European Union's Horizon Europe research and innovation projects 6Green (Grant Agreement no. 101096925) and Exigence (Grant Agreement no. 101139120).
