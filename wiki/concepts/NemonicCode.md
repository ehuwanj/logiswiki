---
title: "Nemonic Code (Spain)"
type: concept
tags: [routing, nemonic-code, spain, label, long-haul]
sources:
  - "[raw/routing/Nemonic Codes - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166052745/Nemonic+Codes)"
last_updated: 2026-05-24
---

## Definition

A Nemonic Code is a three-digit code maintained by GLS Spain (in their Atlas system) and printed on the parcel label. It is used for GLS Spain's national routing of parcels destined for other countries (and includes customs-handling codes for certain flows). Most Nemonic codes are not present in `TBLOCATION.TSPCODE`.

## Key Information

**Source:**
- Excel sheet from GLS Spain (current contact: Eric Gilbert Sanchez).
- Columns: sender zipcode, recipient country, Nemonic code.
- Update frequency: irregular, possibly weekly.

**Loading into Route Finder:**
- New API accepts the full sheet (Excel/CSV/JSON).
- Each load completely replaces prior Nemonic configuration (no incremental updates).
- Atomic-replace is required to avoid gaps for in-flight routing.

**Lookup at routing time:**
- Inputs: `sender.zipcode` + `receiver.CountryCodeA2`.
- Emitted during long haul routing.
- ShipIt consumes the emitted code for the label.

**Historical context:**
- ShipIt and ISRS previously loaded this config one-shot with no UI.
- ISRS targeted Poland additionally; ShipIt did not.

**Open questions:**
- Do other countries need similar functionality?
- Output context inside Shipper Specific Routing still TBD.

## Related Links

- [[RouteFinder]] - implementer
- [[ShipIt]] - consumer (labels)
- [[LongHaulRouting]] - emission phase
- [[OutboundSortingFlag]] - related label-emitted code
- [[ShipperSpecificRouting]] - related discussion thread
- [[nemonic-codes-routing]] - design source
