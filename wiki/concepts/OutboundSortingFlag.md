---
title: "Outbound Sorting Flag (OSF / OSC)"
type: concept
tags: [routing, osf, osc, label, long-haul]
sources:
  - "[raw/routing/Analysis Long Haul Routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166047112/Analysis+Long+Haul+Routing)"
  - "[raw/routing/Business Object Model Leg Based Routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166053238/Business+Object+Model+Leg+Based+Routing)"
  - "[raw/routing/Shipper Specific Routing in Route-Finder - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166091817/Shipper+Specific+Routing+in+Route-Finder)"
last_updated: 2026-05-24
---

## Definition

The Outbound Sorting Flag (OSF), also called Outbound Sorting Information or Outbound Sorting Code (OSC), is the identifier of the last depot before the final depot on a parcel's route. It is one of the key outputs of long haul routing and is printed on the parcel label. Even if a parcel passes through several sorting hubs, only the OSF for the LAST hub is returned because of label space limits. The OSF corresponds to the `TSPCode` of the sorting location.

## Key Information

- Synonyms: OSF (Outbound Sorting Flag), OSC (Outbound Sorting Code), Outbound Sorting Information.
- Source identifier: `TSPCode` on the sorting location.
- Output of [[LongHaulRouting]].
- Found in MDM as `customActivityCode` (e.g., "R45") per [[business-object-model-leg-based-routing]].
- Found in legacy UniQue as `TBROUTE.ROUTEFLAG001`.
- A specific OSC value (e.g., R87) is what motivates [[ShipperSpecificRouting]] bundle routing: routes are deliberately steered through a specific transfer location to emit the desired OSC.

## Related Links

- [[LongHaulRouting]] - emission phase
- [[ShipperSpecificRouting]] - driver for forced-traversal routing
- [[RouteFinder]] - implementer
- [[NemonicCode]] - related Spain-specific label code
- [[MdmLocations]] - holds customActivityCode source
- [[UniQue]] - legacy source (TBROUTE.ROUTEFLAG001)
- [[analysis-long-haul-routing]] - definitional source
- [[shipper-specific-routing-route-finder]] - usage in SSR
