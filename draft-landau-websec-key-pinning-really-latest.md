---
title: Strict Mode for HTTP Public Key Pinning
abbrev: HPKP Strict Mode
docname: draft-landau-websec-key-pinning-really-latest
date: 2015-11-17
category: info

ipr: trust200902
area: General
workgroup: Network Working Group
keyword: Internet-Draft

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
  -
    ins: H. Landau
    name: Hugo Landau
    email: hlandau@devever.net

normative:
  RFC2119:
  RFC7469:

--- abstract

The HTTP Public Key Pinning Extension permits a User Agent to forego Pin
Validation for policy reasons, for example when a server's certificate chain
terminates at a user-defined trust anchor. This facilitates "corporate"
man-in-the-middle attacks which website operators may not wish to facilitate.

This specification specifies an extension to the HTTP Public Key Pinning
specification allowing website operators to request that User Agents impose
Denial of Service in relation to that website when a "policy" man-in-the-middle
attack is detected. This allows such website operators to, by virtue of their
plurality multiplied by their userbase, discourage the use of "policy"
man-in-the-middle attacks.

--- middle

Introduction
============

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED",  "MAY", and "OPTIONAL" in this document are to be
interpreted as described in {{RFC2119}}.

The "strict" Keyword
====================

The "strict" keyword is introduced to the Public-Key-Pinning and
Public-Key-Pinning-Report-Only HTTP headers specified in {{RFC7469}}.

Where the "strict" keyword appears, a User Agent shall record this fact. Where
a User Agent attempts connection to a website which has a pin set recorded
incorporating the "strict" keyword, a User Agent MUST perform Pin Validation as
specified in {{RFC7469}}, and MUST NOT skip Pin Validation for policy reasons
as is otherwise permitted by {{RFC7469}} section 2.6.

Ordinarily, a pin set is not recorded if the connection it is delivered over
would not pass Pin Validation in relation to that pin set. When the "strict"
keyword is used, a pin set is recorded even if it would not pass Pin
Validation.  Since there is not necessarily any way of distinguishing between a
man-in-the-middle attack performed for policy reasons and a website
misconfiguration, this allows website operators to cause total Denial of
Service on their websites. Thus the "strict" keyword has a mandatory parameter
which acts as a max-age value for the "strict" keyword only. This allows the
potential auto-DoS period due to a misconfiguration to be limited.

Because each page load will renew a pin with the "strict" keyword, even a short
"strict" max-age value of e.g. 10 minutes will have the effect of making a site
unusable for victims of "policy" man-in-the-middle attacks.

The parameter value for the "strict" keyword MUST be included.

Security Considerations
=======================

Entities performing man-in-the-middle attacks may trivially strip
Public-Key-Pinning headers, defeating this mechanism. Many such entities may
neglect to perform this practice, or may not have the operational expertise to
configure such measures. The use of preload lists bundled with User Agents
could ensure that strict mode is effective even in the face of such measures.

IANA Considerations
===================

None.

--- back

Pin Examples
============

The following shows an example pinning header, which also enables strict mode
for an hour.

~~~~~~~~~~
Public-Key-Pins: max-age=2592000;
  pin-sha256="E9CZ9INDbd+2eRQozYqqbQ2yXLVKB9+xcprMF+44U1g=";
  pin-sha256="LPJNul+wow4m6DsqxbninhsWHlwfp0JecwQzYpOLmCQ=";
  strict=3600
~~~~~~~~~~

