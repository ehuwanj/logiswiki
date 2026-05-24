---
title: "Business Object Model: Leg Based Routing"
type: source
tags: [routing, leg-based, data-model, mdm]
sources:
  - "[raw/routing/Business Object Model Leg Based Routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166053238/Business+Object+Model+Leg+Based+Routing)"
last_updated: 2026-05-24
---

## Summary

Defines the business object model behind leg-based long-haul routing. The GLS network is modeled as **depots** (locations in MDM), **long haul tour legs** (directed connections between two depots with basic attributes), and **line haul** elements that implement a tour leg (operational view: vehicle, cost, departure/arrival schedule). Location data is consumed from MDM via Kafka topics (chosen over the MDM REST API due to Route Finder's high performance requirements). A mapping service translates legacy UniQue locCodes into MDM locationKeys (e.g., DE 481 -> DE-480).

## Key Claims

- Network = depots + long haul tour legs + line haul elements (one or more per tour leg).
- Location data comes from MDM via Kafka (preferred over REST API for performance), cached locally in Route Finder.
- Mapping UniQue locCode -> MDM locationKey is created when a location/activity with a legacy loccode is created (planned).
- Required location attributes: locationKey, activities, geo-coordinates (longitude/latitude), outboundSortingFlag, restrictions, activity period (only on activity level), locCode, opening hours (not yet available), handling cost per parcel, capacity, means of transport, customs handling, countryCode, partnerCode, economic area.
- A location is active if any of its activities is active.
- Partner-level constraints can apply to locations (e.g., maximum/minimum weight from partner).
- Tour leg attributes: tourLegID (UUID), startLocationKey, endLocationKey, distance (km, >0), internationalFlag, validFrom, validTo, insertUserID/Timestamp, updateUserID/Timestamp.
- Line haul attributes: lineHaulID (UUID), tourLegID, locationKey (affiliation), cost (Euro, >0), vehicle type (enum).
- Line haul departure/arrival time: weekday (MON..SUN), hour (0-23), minute (0-59).

## Evidence and Notes

- MDM Q&A closed: locCode-to-locationKey mapping via dedicated mapping service; HUBs differentiated by activity code (import sorting, export sorting, transit sorting; codes TBD); OutboundSortingFlag found in `customActivityCode` (example: "R45"); no main-location activity; activity period only on activities (none on location).
- MDM Q&A open: whether `customActivityCode` (e.g., "R45") should appear in the routing depends on consumer; opening hours not yet modeled.
- LU locations physically located in BE but belonging to LU (per UniQue) - reinforces the importance of distinct countryCode + partnerCode attributes.

## Related Links

- [[LegBasedRouting]] - concept page
- [[TourLeg]] - data model entity
- [[LineHaul]] - data model entity
- [[MdmLocations]] - source of location data
- [[OutboundSortingFlag]] - found in customActivityCode
- [[RouteFinder]] - consumer of this model
- [[network-leg-based-routing]] - network construction from UniQue
- [[long-haul-routing-national-international-partners]] - routing scope
