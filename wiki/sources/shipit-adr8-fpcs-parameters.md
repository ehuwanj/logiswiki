---
title: "ShipIT ADR8: Handle Specific FPCS Parameters Without Shipper Context"
type: source
tags: [source, shipit, farm, parameters, configuration, adr]
sources:
  - "[raw/shipit/ADR8 Handle specific FPCS parameters without shipper context - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200843125/ADR8+Handle+specific+FPCS+parameters+without+shipper+context)"
last_updated: 2026-05-16
---

## Summary

Some FPCS parameters need different values per customer but the requests that use them carry no shipper context (e.g., parcel-shop search requests). A mechanism is needed beyond the existing shipper-specific parameter overrides. Decision: Option 2 - user-specific parameters, where the authenticated user's identity determines which parameter value to apply.

## Key Claims

- Decision: Option 2 - user-specific parameters using authenticated user context.
- Affected parameters: PARCELSHOP_MIN_AVAILABILITY_DAYS, PARCELSHOP_MIN_AVAILABILITY_PERCENTAGE, PARCELSHOP_VALIDATION_ACTIVE_BY_COUNTRY.
- Rationale: user context is always available for all requests; parameters can be enforced transparently without API changes.
- Option 1 (optional shipper-id request parameter): rejected to avoid API changes that clients might forget to use.
- Making the optional parameter mandatory in option 1 was also rejected to prevent breaking API changes.
- Decision opens up future possibilities for user-level individual configuration of other parameters.

## Evidence and Notes

- Existing mechanism: shipper-specific parameters assign different FPCS values per shipper.
- Implementation effort for Option 2 is higher: user context must be injected into parameter evaluation; additional DB table likely needed.
- Different Farm instances are not required; one Farm can serve different configurations per user.

## Related Links

- [[ShipItFarm]] - Farm context
- [[AttributeStore]] - provides user identity context
- [[shipit-adr4-oauth2-integration]] - OAuth2 user authentication
