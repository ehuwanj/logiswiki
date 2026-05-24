---
title: "Analysis: UniQue Tour Management"
type: source
tags: [routing, tour-management, unique, isrs-20511]
sources:
  - "[raw/routing/Analysis Unique Tour Management - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166043448/Analysis+Unique+Tour+Management)"
last_updated: 2026-05-24
---

## Summary

Analysis of how tours are managed in the legacy UniQue system (ISRS-20511). Two configuration paths exist: (1) Tour planning via the UniQue Office tour-management GUI with automatic file generation/upload via a daily cron (spmd210, 18:30 weekdays) for Germany, Austria, Ireland, Poland; (2) Manual upload of tour files to an FTP server. New locations referenced in tour files must first be set up via the MDU file process for new locations before the tour file is imported. Tour file format example: `276|3000|DE 300|01257|4711|HANNOVER||1|N|`.

## Key Claims

- Two tour configuration paths in UniQue: GUI-driven (Office Tour Management) and manual FTP upload.
- Office Tour Management is used by Germany, Austria, Ireland, and Poland.
- Cron job `spmd210` runs on weekdays at 18:30 to generate and upload tour files for those countries.
- Each location (main + sub) maintains its own tours; rights are assigned per location.
- The only zip-code-to-tour assignment that can be changed is the assignment itself - zipcodes themselves are not maintained here.
- Tour file format columns include country (276 = DE), zip-area, location code, postal code, customer id, city, etc.
- New locations must be set up via the MDU process before tour file import - otherwise tour rows referencing them are rejected.
- Contact for process check: Carsten Ebert (Region Ost, D10/12).

## Evidence and Notes

- Example tour data line: `276|3000|DE 300|01257|4711|HANNOVER||1|N|`.
- Tours table TBTOURM is the source for the GUI; replicated into TBTOUR (see [[nearest-zipcode-match-comparison]]).
- Countries in TBTOURM (GUI-managed): Austria, Belgium, Denmark, France, Germany, Ireland, Monaco, Poland.

## Related Links

- [[UniQue]] - legacy system
- [[TourManagement]] - tour management concept
- [[nearest-zipcode-match-comparison]] - TBTOUR/TBTOURM details
- [[migration-concept-routing]] - mentions tour import/export
- [[RouteFinder]] - destination for new tour data
