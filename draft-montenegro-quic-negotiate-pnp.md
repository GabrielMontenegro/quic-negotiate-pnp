---
title: QUIC Negotiation for Packet Header Protection
abbrev: QUIC PkHP Negotiation
docname: draft-montenegro-quic-negotiate-pnp-latest
date: {DATE}
category: info
ipr: trust200902
area: Transport
workgroup: QUIC
keyword: Internet-Draft

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
 -
    ins: G. Montenegro
    name: Gabriel Montenegro
    organization: Microsoft Corporation
    email: Gabriel.Montenegro@Microsoft.com
 -
    ins: N. Banks
    name: Nick Banks
    organization: Microsoft Corporation
    email: NiBanks@Microsoft.com
 -
    ins: P. Balasubramanian
    name: Praveen Balasubramanian
    organization: Microsoft Corporation
    email: PravB@Microsoft.com

normative:

  QUIC-TRANSPORT:
    title: "QUIC: A UDP-Based Multiplexed and Secure Transport"
    date: {DATE}
    seriesinfo:
      Internet-Draft: draft-ietf-quic-transport-latest
    author:
      -
        ins: J. Iyengar
        name: Jana Iyengar
        org: Fastly
        role: editor
      -
        ins: M. Thomson
        name: Martin Thomson
        org: Mozilla
        role: editor

  QUIC-TLS:
    title: "Using Transport Layer Security (TLS) to Secure QUIC"
    date: {DATE}
    seriesinfo:
      Internet-Draft: draft-ietf-quic-tls-latest
    author:
      -
        ins: M. Thomson
        name: Martin Thomson
        org: Mozilla
        role: editor
      -
        ins: S. Turner
        name: Sean Turner
        org: sn3rd
        role: editor


informative:


--- abstract

This document defines an extension to reduce the cost of QUIC deployment in environments like datacenters by 
allowing packet header protection to be optionally disabled.

--- note_Note_to_Readers

Discussion of this draft takes place on the QUIC working group mailing list
(quic@ietf.org), which is archived at
<https://mailarchive.ietf.org/arch/search/?email_list=quic>.

Working Group information can be found at <https://github.com/quicwg>; source
code and issues list for this draft can be found at
<https://github.com/quicwg/base-drafts/labels/-recovery>.

--- middle

# Introduction

QUIC is a new transport for the internet. In its generality, there are features which are not well suited
for some environments. In particular, QUIC uses Packet Header Protection (PkHP) 
to prevent ossification and to provide unlinkability upon (voluntary) migration.
However, there are environments where these are not a concern, in particular,
connections within a datacenter.

This document defines a negotiation mechanism using transport parameters to disable PkHP.
Internet facing nodes SHOULD NOT disable PkHP, so
browsers, for example, should not implement this extension. On the other hand, configured nodes
within a datacenter could turn off PkHP in their exchanges to avoid the CPU cost that PkHP implies.

# Conventions and Definitions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 {{!RFC2119}} {{!RFC8174}}
when, and only when, they appear in all capitals, as shown here.

# Transport Parameter to Disable Packet Header Protection

This document defines a new transport parameter for QUIC {{QUIC-TRANSPORT}}:

disable_packet_header_protection (0x000c ?, value TBD):

: The endpoint is disabling packet header protection as specified in {{QUIC-TLS}}.
  This parameter is a zero-length value. This parameter only affects short headers.

A successful negotiation of the "disable_packet_header_protection" parameter
requires both peers to send this transport parameter as well as the "disable_migration"
parameter.

An endpoint MUST treat receipt of "disable_packet_header_protection" without the
"disable_migration" parameter as a connection error of type TRANSPORT_PARAMETER_ERROR.

Peers that have successfully negotiated the "disable_packet_header_protection" parameter
MUST NOT use packet header protection on short header packets.


# Security Considerations

PkHP was added as a partial mitigation against linkability, and to prevent ossification.
The "disable_packet_header_protection" parameter should be negotiated in environments in which these are not
a concern.

# IANA Considerations

Per section 10 of {{QUIC-TLS}}, this document requests IANA assign a value for the new transport parameter
and record it in the registry for "QUIC Transport Parameters" under the "QUIC Protocol" heading.
IANA is further requested to assign a value with the first byte in the range 0x00 to 0xfe (in
hexadecimal) as follows:


| Value  | Parameter Name                   | Specification                       |
|:-------|:---------------------------------|:------------------------------------|
| 0x000c | disable_packet_header_protection | This document                       |


--- back

# Acknowledgments
{:numbered="false"}

Thanks to the following individuals for useful discussions: Christian Huitema, Martin Thomson,
Mikkel Fahnøe Jørgensen, Ian Swett, Martin Duke, Lucas Pardue.
