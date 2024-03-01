---
v: 3

title: Scalable Remote Attestation for Systems, Containers, and Applications
abbrev: SRASCA
docname: draft-moriarty-attestationsets-latest
cat: std
consensus: yes
submissiontype: IETF
number:
date:
consensus: true
area: Security
keyword:


venue:
  group: "Remote ATtestation ProcedureS (rats)"
  type: "Working Group"
  mail: "rats@ietf.org"
  arch: https://mailarchive.ietf.org/arch/browse/rats/
  github: aj-stein-nist/draft-moriarty-attestationsets
  latest: https://aj-stein-nist.github.io/draft-moriarty-attestationsets/draft-moriarty-attestationsets.html

author:
- name: Kathleen M. Moriarty
  org: Center for Internet Security
  abbrev: CIS
  email: kathleen.Moriarty.ietf@gmail.com
  street: 31 Tech Valley Drive
  code: '12061'
  city: East Greenbush
  region: NY
  country: USA
- name: Antonio Fontes
  org: Dell Technologies
  abbrev: Dell
  email: antonio.fontes@dell.com
  street: 176 South Street
  code: '01748'
  city: Hopkinton
  region: MA
  country: USA

normative:
  RFC2119:
  RFC8392:
  RFC7519:

informative:
  I-D.ietf-rats-eat:

--- abstract

This document establishes an architectural pattern whereby a remote attestation could be issued for a complete set of benchmarks or controls that are defined and grouped by an external entity, preventing the need to send over individual attestations for each item within a benchmark or control framework.
This document establishes a pattern to list sets of benchmarks and controls within CWT and JWT formats for use as an Entity Attestation Token (EAT).

--- middle

# Introduction

Posture assessment has long been desired, but has been difficult to achieve due to complexities of customization requirements at each organization.
By using policy and measurement sets that may be offered at various assurance levels defined in an Entity Attestation Token (EAT) profile [I-D.ietf-rats-eat], automating posture assessment through attestation becomes achievable for organizations of all sizes.
The measurement and policy groupings in an EAT profile may be provided by the vendor or by a neutral third party to enable ease of use and consistent implementations.
This provides simpler options to enable posture assessment at selected levels by organizations without the need to have in-house expertise.
The measurement and policy sets may also be customized, but not necessary to achieve posture assessment to predefined options.
This document describes a method to use existing attestation formats and protocols while allowing for defined profiles of policies, benchmarks, and measurements for specific assurance levels that scale to provide transparency to posture assessment results summarized with remote attestation.

By way of example, the Center for Internet Security (CIS) hosts recommended configuration settings to secure operating systems, applications, and devices in CIS Benchmarks developed with industry experts.
Attestations aligned to the CIS Benchmarks or other configuration guide such as a DISA STIG could be used to assert the configuration meets expectations.
This has already been done for multiple platforms to demonstrate assurance for firmware according to NIST SP 800-193, Firmware Resiliency Guidelines.  In order to scale remote attestation, a single attestation for a set of Benchmarks or policies being met may be sent to the remote atteststaion management system.
On traditional servers, assurance to NIST SP 800-193 is provable through attestation from a root of trust (RoT), using the Trusted Computing Group (TCG) Trusted Platform Module (TPM) chip and attestation formats.

At boot, policy and measurement expectations are verified against a set of "golden policies" from collected and attested evidence.  Device identity and measurements can also be attestated at runtime.  The attestations on evidence (e.g. hash of boot element) and verification of attestations are typically contained within a system and are limited to the control plane for
management.
The policy and measurement sets for comparison are protected to assure the result in the attestation verification process for boot element.
Event logs and PCR values may be exposed to provide transparency into the verified attestations.  Remote attestation provides a summary of a local assessment of posture for managed systems and across various layers (operating system, application, containers) in each of these systems in a managed environment.

There is a balance of exposure and evidence needed to assess posture when providing assurance of controls and system state.
Currently, logs and TPM PCR values may be passed to provide assurance of verification of attestation evidence meeting set requirements.
Providing the assurance can be accomplished with a remote attestation
format such as the Entity Attestation Token (EAT) [I-D.ietf-rats-eat]
and a RESTful interface such as ROLIE or RedFish.
Policy definition blocks may be scoped to control measurement sets, where the EAT profile asserts compliance to the policy or measurement block specified and may include claims with the log and PCR value evidence.
Measurement and Policy sets in an EAT profile may be published and
maintained by separate entities (e.g.  CIS Benchmarks, DISA STIGs).
The policy and measurement sets should be maintained separately even if associated with the same benchmark or control set.
This avoids the need to transition the verifying entity to a remote system for individual policy and measurements which are performed locally for more immediate remediation as well as other functions.

Examples of measurement and policy sets that could be defined in EAT
profiles include, but are not limited to:

- Hardware attribute certificates, TCG
- Hardware Attribute Certificate Comparison Results, TCG
- Reference Integrity Measurements for firmware, TCG
- Operating system benchmarks at Specified Assurance Levels, CIS
- Application hardening Benchmarks at Specified Assurance Levels, CIS, DISA STIG
- Container security benchmarks at Specified Assurance Levels, CIS

Scale, ease of use, full automation, and consistency for customer consumption of a remote attestation function or service are essential toward the goal of consistently securing systems against known threats and vulnerabilities.
Mitigations may be baked into policy.
Claim sets of measurements and policy verified to meet or not meet
<xref target="I-D.ietf-rats-eat"> Endorsed values </xref> are
conveyed in an Entity Attestation Token made available to a RESTful
interface in aggregate for the systems managed.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Policy and Measurement Set Definitions

This document defines EAT claims in the JWT [RFC7519] and CWT [RFC8392] registries to provide attestation to a set of verified claims within a defined grouping.
The trustworthiness will be conveyed on original verified evidence as well as the attestation on the grouping.

| Claim | Long Name                  | Description                      | Format
|---
| MPS   | Measurement or Policy Set  | Name for the MPS                 |
| LEM   | Log Evidence of MPS        | Log File or URI                  |
| PCR   | TPM PCR Values             | URI                              |
| FMA   | Format of MPS Attestations | Format of included attestations  |
| HSH   | Hash Value/Message Digest  | Hash value of claim-set          |

# Supportability and Re-Attestation

The remote attestation framework shall include provisions within the system and attestation authority to allow for Product modification.

Over its lifecycle, the Product may experience modification due to: maintenance, failures, upgrades, expansion, moves, etc..

The customer can chose to:

- Run remote attestation after product modification, or
- Not take action and remain un-protected

In the case of Re-Attestation:

- framework needs to invalidate previous TPM PCR values and tokens,
- framework needs to collect new measurements,
- framework needs to maintain history or allow for history to be logged to enable change traceability attestation, and
- framework needs to notify that the previous attestation has been invalidated

# Configuration Sets

In some cases, it may be difficult to attest to configuration settings for the initial or subsequent attestation and verification processes.
The use of an expected hash value for configuration settings can be used to compare the attested configuration set.
In this case, the creator of the attestation verification measurements would define a set of values for which a message digest would be created and then signed by the attestor.
The expected measurements would include the expected hash value for comparison.
The configuration set could be the full attestation set to a Benchmark or a defined subset.

# Remediation

If policy and configration settings or measurements attestated do not meet expected values, remediation is desireable.
Automated remediation performed with alignment to zero trust architecture principles would require that the remeidation be performed prior to any relying component executing.
The relying component would verifiy before continuing in a zero trust architecture.

Ideally, remediation would occur on system as part of the process to attest to a set of attestations, similar to how attestation is performed for firmware in the boot process.
If automated remediation is not possible, an alert should be generated to allow for notification of the variance from expected values.

# Security Considerations

This document establishes a pattern to list sets of benchmarks and controls within CWT and JWT formats.
The contents of the benchmarks and controls are out of scope for this document.
This establishes an architectural pattern whereby a remote attestation could be issued for a complete set of benchmarks or controls as defined and grouped by external entities, preventing the need to send over individual attestations for each item within a benchmark or control framework.
This document does not add security consideration over what has been described in the EAT, JWT, or CWT specifications.

# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
