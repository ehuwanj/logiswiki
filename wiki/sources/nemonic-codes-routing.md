---
title: "Nemonic Codes (Spain)"
type: source
tags: [routing, nemonic-codes, spain, long-haul, isrs-13903]
sources:
  - "[raw/routing/Nemonic Codes - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166052745/Nemonic+Codes)"
last_updated: 2026-05-24
---

## Summary

Analysis (ISRS-13903) of how to support Nemonic Codes - three-digit codes used by GLS Spain for national routing of parcels sent from Spain to another country. These codes are partly derived from UniQue outbound sorting codes but also include Spain-specific codes maintained in their Atlas system (for customs handling, etc.). Most Nemonic codes do not exist in `TBLOCATION.TSPCODE`. They are printed on the label. The plan: Route Finder accepts a full Excel/CSV/JSON dump from GLS Spain via a new API, replacing prior config entirely; Route Finder emits the Nemonic Code during long haul routing based on sender zipcode and receiver country code (CountryCodeA2); ShipIt then includes it in label data.

## Key Claims

- Nemonic codes are three-digit codes maintained by GLS Spain in their Atlas system.
- They are printed on the label and used for routing parcels sent from Spain to other countries.
- Most Nemonic codes are NOT found in TBLOCATION.TSPCODE.
- Source data: Excel sheet (e.g., ASOS_Routing.xlsx) provided by Eric Gilbert Sanchez at GLS Spain; columns include sender zip code, recipient country, Nemonic code.
- Update frequency: irregular, possibly weekly.
- For ShipIt and ISRS, the load was previously one-shot without a UI. Poland was an extra target for ISRS but not ShipIt; otherwise config was the same.
- For Route Finder, the plan: accept an Excel/CSV/JSON dump via API; on each load, replace all prior Nemonic config in full.
- Route Finder uses sender.zipcode + receiver.CountryCodeA2 to emit the corresponding Nemonic code during long haul routing.
- ShipIt consumes the Nemonic code to include on labels.

## Evidence and Notes

- Open question: do other countries need this functionality?
- Open question: how to ensure no gap in Nemonic code availability during config exchange (atomic replace required).
- Output context for the Nemonic code is still to be discussed in the Shipper Specific Routing context.

## Related Links

- [[NemonicCode]] - concept page
- [[RouteFinder]] - implementer
- [[ShipIt]] - consumer (labels)
- [[LongHaulRouting]] - routing context where the code is emitted
- [[OutboundSortingFlag]] - related label-emitted code
- [[shipper-specific-routing-route-finder]] - related discussion thread
