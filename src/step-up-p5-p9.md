---
layout: base.njk
title: Medium (P5) to high (P9) verification step-up journey 
---

NHS login supports multiple levels of identity verification (low, medium and high). These are based upon [DCB3051 Identity Verification and Authentication Standard for Digital Health and Care Services](http://digital.nhs.uk/isce/publication/dcb3051).

### Medium level verification

For medium level identity verification (P5) users are expected to provide only basic identity related information, such as their date of birth and NHS number. Users who do not know their NHS number can use their full name and postcode instead.     

This information is checked to correspond to a record on PDS (Personal Demographic Service) and confirm the user’s identity to medium level. This level of verification minimises friction within registration processes, often experienced by partners that deliver services with non-sensitive data.

If required, NHS login offers partners functionality to initiate the step-up journey from medium level (P5) to high level (P9) verification.

---

## Partner service provides access to both basic data and sensitive data

A service may offer multiple features to users, where some require basic user data and others require sensitive user data.

The service would request NHS login for medium level verification (P5) and high level authentication. Successfully authenticated users can be either medium (P5) or high level (P9) in terms of identity verification.

With high level verification (P9) the service can offer all functionalities, as the user meets the service requirements of high verification and high authentication. The service can only offer basic features to users with medium level verification (P5) at their initial login, as they meet the service requirements of medium verification and high authentication only.

When a user with medium level verification attempts to access other features supported by sensitive data, the service then requires high verification and high authentication. The service prompts the user to undertake a verification step-up journey via NHS login, which will take the user verification level from medium to high.

---

## Technical details 

### Identity proofing level

The `identity_proofing_level` describes the maximum identity verification level the user has achieved. The claim is included in both the ID token and userinfo when requesting the `profile` scope.

It enables relying parties requesting medium level verification (P5) to determine if other claims returned have been validated to high level (P9), or simply claimed by a medium level (P5) user. This also enables relying parties to initiate the step-up journey from medium to high level verification.

### Vector of Trust (VoT)

A service will request a set of acceptable VoT values. This should contain one value for the identity verification component, combined with one or more values for the authentication component. The service should request the lowest level of acceptable identity verification within the VoT set.

Example of acceptable vectors in the ‘vtr’ parameter for a service requesting medium level verification:
`[“P5.Cp.Cd”, “P5.Cp.Ck”,“P5.Cm”]`

Example of acceptable vectors in the ‘vtr’ parameter for the step-up from medium to high level verification:
`[“P9.Cp.Cd”, “P9.Cp.Ck”,“P9.Cm”]`

### Requested scopes and claims returned

When a partner service requests medium level identity verification in the ‘vtr’ parameter, successfully authenticated users can either be P5 or P9.

For high level verified users (P9), all claims corresponding to requested scopes will be populated according to the rules described in the [external interface specification](https://nhsconnect.github.io/nhslogin/interface-spec-doc/).

However, for medium level verified users (P5), not all claims corresponding to requested scopes will be populated, as the user has not been verified to the sufficient level required.

### Single Sign-On (SSO)

NHS login supports two parameters for `asserted_login_identity` and prompt as part of the OIDC flow. This allows users to seamlessly access the identity verification step-up journey from a partner service, without the need for re-authentication.

1. `asserted_login_identity` will be a querystring parameter 
2. parameter content is a signed jwt
3. the `asserted_login_identity` token is a one-time token (TTL 60 sec)

### Flow diagram

![diagram](nhslogin/images/P5-P9Step-upflow_small.png "P5 - P9 Step up verification flow")
