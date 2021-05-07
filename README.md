# Healthcare Trustmark


This document was created to standardize the way Identity Assurance Levels are communicated electronically.  It is based on
NIST SP 800-63-3 (Digital Identity Guidelines), where Identity Assuance Levels are defined as `1`, `2`, or `3`.  The Trustmark adds one additional Identity Assurance Level, `1.5`.  `1.5` is a level to signify some corroborating evidence of identity is given but the Identity Assurance Level does not meet Level `2` as defined by NIST SP 800-63-3 (Digital Identity Guidelines).



The Trustmark Details (Mapping to NIST 800-63-3 and TH 1.5)
------------------------------------------------------------

| Identity Assurance Level `IAL` Value                        | VoT Identity Proofing `P` Value                      |
| ----------------------------------------------------------- | ---------------------------------------------------- |
| [IAL1](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4) | [P1](https://tools.ietf.org/html/rfc8485#section-2.1)|       
| [IAL1.5](https://github.com/TransparentHealth/healthcare-trustmark/blob/main/ial-1-5-definition.md) | [P2](https://tools.ietf.org/html/rfc8485#section-2.1)|
| [IAL2](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4) | [P2](https://tools.ietf.org/html/rfc8485#section-2.1)|
| [IAL3](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4) | [P3](https://tools.ietf.org/html/rfc8485#section-2.1)|


| Autheticator Assurance Level `AAL` Value                    | VoT Credential `C` Value                             |
| ----------------------------------------------------------- | ---------------------------------------------------- |
| [AAL1](https://pages.nist.gov/800-63-3/sp800-63b.html#sec4) | [C1](https://tools.ietf.org/html/rfc8485#section-2.2)|
| [AAL2](https://pages.nist.gov/800-63-3/sp800-63b.html#sec4) | [C2](https://tools.ietf.org/html/rfc8485#section-2.2)|   
| [AAL3](https://pages.nist.gov/800-63-3/sp800-63b.html#sec4) | [C3](https://tools.ietf.org/html/rfc8485#section-2.2)|   
 


Details on Use
--------------

While designed with OpenID Connect in mind, this Trustmark can be applied to other systems including LDAP, SAML, FHIR, or user profile calls using OAuth2.  For example, a user profile request may include the name\value pair `vot`=`P2`, to indicate a user has undergone identity proofing to a NIST Identity Assurance Level of IAL `2`. Communicating an `IAL` of `1`,,`1.5`, `2`, or `3` was the motivation of this document, but it also provides a basic mapping for `AAL` and `FAL`. See scope and security sections for use and implementation guidance.



URL
---

The [URL](https://github.com/TransparentHealth/800-63-3-trustmark/) to this public Github repository serves as the Trustmark itself.  The value of the `vtm` field shall be `https://github.com/TransparentHealth/healthca-trustmark/`.

Scope
-----


The is a [Vectors of Trust Trustmark](https://tools.ietf.org/html/rfc8485) based on 
[NIST SP 800-63-3 Digital Identity Guidelines](https://pages.nist.gov/800-63-3/).  It adds one additional Identity Assurance Level to accomidate health care scenerios.


It was created as part of an effort to establish a best practice for sharing digital identities in a healthcare setting. Entities who are operating or implementing [OpenID Connect](https://openid.net/connect/) Identity Providers(IdP) may use this as an implementation guide. While designed with US healthcare in mind, it is not healthcare specific.


* Identity Assuance Levels (IAL) as defined in ([NIST SP 800-63A: Enrollment and Identity Proofing](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-63a.pdf))
* Autheticator Assurance Levels (AAL) as defined in ([NIST SP 800-63B: Authentication and Lifecycle Management](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-63b.pdf))

Security Considerations
-----------------------

While communicating IAL is acceptable within a user profile API call, communicating AAL in this way should be avoided.  This is because the user profile API call can be made without the user being present. AAL, therefore, should be communicated within an `id_token` or in some manner which verifies  that the user is actually present.  The value "C2.P2" is would be appropriate in an `id_token` but not in the response from a user profile endpoint.  In a user profile endpoint, a value of `vot="P2"` would be acceptable, but a value incluing authenticator assurance information (e.g. `vot="P2.C2"`) should be avoided.


How To Use this Trustmark within an Identity Provider (IdP) That Your Organization Operates
-------------------------------------------------------------------------------------------


OpenID Connect Provider
_______________________


Whithin your OpenID Connect Identity Provider, include the folling value in the ink to this repository shall be used as the value of the `vtm` claim. The `vot`claim must also be present. The folowing is an example  OpenID Connect `id_token` which is provided to relying parties updon successful authentication.:

    {
    "issuer": "https://oidc.example.com",
    "sub": "12345678901234",
    "given_name": "James",
    "family_name": "Kirk",
    ...
    "vot": "P2.C2",
    "vtm": "https://github.com/TransparentHealth/healthcare-trustmark/",
    ...
    }

Other Identity Providers
________________________


The `vot` value for identity assurance may also be stored in LDAP, Active Directory, or SAML. The document's only guideance is to use the field name `vot` and to include the value `P1`, `P2`, or `P3`.


How To Use This Trustmark as a Relying Party (i.e. as a client )
----------------------------------------------------------------

Isolate the value of  `vot` to make informed decisions on what a user should and shouldn't be allowed to do.

For example, you may want to limit release health information to the users with an Identity proofing of at least 2.

Below is a pseudocode illustation


    payload = get_id_token_payload(idp_callback_verified_response)

    if "P2" not in payload["vot"] or "P3" not in payload["vot"]: 
        # Deny Access or begin an ID proofing event
    else:
        # Grant access to a resource.
        # Example: Allow Access to an authorization endpoint(to get an oauth2 token).
        # Example: Health data as PDF or JSON




Reference List
--------------

* [NIST SP 800-63-3 Digital Identity Guidelines Homepage](https://pages.nist.gov/800-63-3/) 
* [RFC 8485 Vectors of Trust](https://tools.ietf.org/html/rfc8485)
* [OpenID Connect Profile for Consumer Health](https://github.com/TransparentHealth/openid-connect-consumerhealth-profile/blob/master/README.md)
* [HEART WG](https://openid.net/wg/heart/)

