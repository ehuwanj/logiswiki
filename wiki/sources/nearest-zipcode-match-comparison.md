---
title: "Nearest Zipcode Match: Unique vs ShipIt vs RTG"
type: source
tags: [routing, zipcode, nearest-match, unique, shipit, isrs-6183]
sources:
  - "[raw/routing/Nearest zipcode match (comparison between Unique, ShipIt and RTG) - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166046677/Nearest+zipcode+match+comparison+between+Unique+ShipIt+and+RTG)"
last_updated: 2026-05-24
---

## Summary

Comparison (ISRS-6183) of how UniQue, ShipIt, and Route Finder (RTG) treat zipcodes during tour lookup. UniQue performs detailed format validation and length normalization via TBZIPFORMAT (per-country: MINZIPLENGTH, MAXZIPLENGTH, ZIPTOURLENGTH, NEARESTFLAG, ZIPFORMAT) before doing the tour search, and falls back to a default zipcode (TBCONVERT convtype 20365 / 50607) when no match is found. ShipIt delegates routing to UniQue but does no detailed validation (only strips spaces and validates UK formats). RTG uses ZIPTOURLENGTH to compute a prefix and does no detailed format validation; nearest-match logic = largest tour_zipcode <= consignee_zipcode, else smallest >= consignee_zipcode, then default zipcode if still no match. Ireland has special virtual codes (Eircode-derived) that ShipIt replicates locally in Postalarea.

## Key Claims

- UniQue zipcode handling lives in GLRO010 (calculateRouting) / GLRO010A / GLRO01B (formatting+validation) / GLRO018B (UK-special formatting).
- TBZIPFORMAT.ZIPFORMAT description characters: `$` = letter, `#` = digit, `!` = any character, literal otherwise; exception `*UK*` triggers UK-specific formatting.
- UK formatting: convert to upper case, strip blanks, add blank before the last digit (CB22 4RG flow).
- Default zipcode determination uses TBCONVERT convtype 20365 (NEAREST) and 50607 (EXACT). Note: 50607 currently has no entries in TBCONVERT.
- ShipIt does not format/validate zipcodes - just strips spaces; rejects unexpected characters; validates UK format.
- ShipIt routing uses TBTOUR data (replicated via shipit.landingzone.tbtour); calculates the formatted zipcode is returned in `zipCode` of the persistence layer.
- RTG uses TBZIPFORMAT.ZIPTOURLENGTH to compute the prefix to match against; multiple matches resolved by largest tour_zipcode <= consignee_zipcode, else smallest >= consignee_zipcode, else default routing per destination/origin country.
- TBTOURM (GUI source) replicates to TBTOUR (used by GLS); only countries with GLSCountry='Y' are active.
- TBTOURM versioning: negative = archived, 0 = current/released, positive = stored-but-not-released.
- Ireland uses virtual codes derived from Eircode routing key + abbreviated city/village (e.g., A63 + Ashford -> WICKASH); maintained in TBCONVERT convtype 50236.

## Evidence and Notes

- TBTOURM countries: Austria, Belgium, Denmark, France, Germany, Ireland, Monaco, Poland.
- TBTOUR countries: 47 (Andorra, Austria, ..., Vatican City).
- ShipIt also creates virtual codes for Ireland but uses local table `Postalarea` (PostalAreaDao.java, getZipCodeMappingIfApplicable in RoutingManager.java).
- Eircode license available locally in GLS Ireland but no validation is planned for UniQue.
- Convtype 50607 is a new code introduced for US default-zipcode handling but is unused (no rows in TBCONVERT).

## Related Links

- [[NearestZipcodeMatch]] - concept page
- [[ShipIt]] - delegates routing to UniQue
- [[UniQue]] - core implementation
- [[RouteFinder]] - new implementation (RTG)
- [[TBZIPFORMAT]] - per-country format table
- [[Eircode]] - Irish zipcode handling
- [[ShortHaulRouting]] - routing context
- [[TourManagement]] - tour source data
- [[analysis-unique-tour-management]] - tour management context
