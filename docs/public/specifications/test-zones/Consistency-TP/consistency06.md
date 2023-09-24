# Specification of test zones for CONSISTENCY06


## Table of contents

* [Background](#background)
* [Test Case](#test-case)
* [Test scenarios](#test-scenarios)
* [Test zone names](#test-zone-names)
* [Test scenarios and message tags](#test-scenarios-and-message-tags)
* [Zone setup for test scenarios]


## Background

See the [test zone README file].


## Test Case
This document specifies defined test zones for test case [CONSISTENCY06].


## Test scenarios

The purpose of the test scenarios is to cover all reasonable contexts where
different message tags are outputted when [CONSISTENCY06] is run on a test zone.
The message tags are defined in the test case ([CONSISTENCY06]) and the scenarios
are defined below.

The test scenarios are structured as stated in the [test zone README file].

## Test zone names

The test zone for each test scenario in this document is a subdomain delegated
from the base name (`consistency06.xa`) and that subdomain having the same name as the
scenario. The names of those zones are given in section
"[Zone setup for test scenarios]" below.


## Test scenarios and message tags

If a message tag is not listed for the scenario, its presence or non-presence is
irrelevant to the test scenario and must be ignored.

Scenario name             | Mandatory message tag                | Forbidden message tags
:-------------------------|:-------------------------------------|:-------------------------------------------
ONE-SOA-MNAME-1           | ONE_SOA_MNAME                        | NO_RESPONSE, NO_RESPONSE_SOA_QUERY, MULTIPLE_SOA_MNAMES
ONE-SOA-MNAME-2           | ONE_SOA_MNAME, NO_RESPONSE           | NO_RESPONSE_SOA_QUERY, MULTIPLE_SOA_MNAMES
ONE-SOA-MNAME-3           | ONE_SOA_MNAME, NO_RESPONSE_SOA_QUERY | NO_RESPONSE, MULTIPLE_SOA_MNAMES
MULTIPLE-SOA-MNAMES-1     | MULTIPLE_SOA_MNAMES                  | NO_RESPONSE, NO_RESPONSE_SOA_QUERY, ONE_SOA_MNAME
MULTIPLE-SOA-MNAMES-2     | MULTIPLE_SOA_MNAMES,NO_RESPONSE      | NO_RESPONSE_SOA_QUERY, ONE_SOA_MNAME
NO-RESPONSE               | NO_RESPONSE                          | NO_RESPONSE_SOA_QUERY, MULTIPLE_SOA_MNAMES, ONE_SOA_MNAME


## Zone setup for test scenarios

Assumptions for the scenario specifications unless otherwise specified for
the specific scenario:
* For each scenario zone there are two name server configured.
  * Both NS (ns1 and ns2) are equal in delegation and in zone.
  * Both NS are in-bailiwick
  * Both NS have both IPv4 and IPv6 addresses
  * All required glue are present in the delegation.
  * All NS IP addresses respond with identical zone content.
* All queries for SOA are responded with a SOA record in an
  authoritative answer.
* ns1 and ns2 respond with identical SOA records.
* All responses, to zone content, are authoritative with
  [RCODE Name] "NoError"
* EDNS, version 0, is included in all responses on queries with EDNS.
* EDNS is not included in responses on queries without EDNS.

### ONE-SOA-MNAME-1
The "happy path". Everything is fine.
* Zone: "one-soa-mname-1.consistency06.xa."

### ONE-SOA-MNAME-2
Not so "happy path". One name server does not respond.
* Zone: "one-soa-mname-2.consistency06.xa."
  * ns1 gives no response at all.

### ONE-SOA-MNAME-3
Not so "happy path". One name server responds without SOA
* Zone: "one-soa-mname-3.consistency06.xa."
  * ns1 responds, but with no SOA record in the answer section
    (maybe answering but not having the zone).

### MULTIPLE-SOA-MNAMES-1
Different SOA MNAME on the servers
* Zone: "multiple-soa-mnames-1.consistency06.xa."
  * MNAME in SOA on ns1 equal to ns1
  * MNAME in SOA on ns2 equal to ns2

### MULTIPLE-SOA-MNAMES-2
Different SOA MNAME on two servers and a third not responding server
* Zone: "multiple-soa-mnames-2.consistency06.xa."
  * MNAME in SOA on ns1 equal to ns1
  * MNAME in SOA on ns2 equal to ns2
  * Also delegated to ns3, for which there is no response.

### NO-RESPONSE
No name server responds.
* Zone: "no-response.consistency06.xa."
  * ns1 gives no response at all.
  * ns2 gives no response at all.


[CONSISTENCY06]:                                                  ../../tests/Consistency-TP/consistency06.md
[RCODE Name]:                                                     https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml#dns-parameters-6
[Test zone README file]:                                          ../README.md
[Zone setup for test scenarios]:                                  #zone-setup-for-test-scenarios

