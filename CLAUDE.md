# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Static Catalan-language site showing Monbus (bus) and FGC (R6 train) timetables between Igualada and Barcelona. No build, no dependencies, no tests, no package manager: open `index.html` in a browser to run it. Deployed via GitHub Pages from the repo root (see [README.md](README.md)).

## Architecture

Everything lives in the single [index.html](index.html) (inline `<style>` and one inline `<script>`, `'use strict'`). The two PDFs in the root (`Horaris-Monbus-2026.pdf`, `R6_Igualada_LA_octubre_20.pdf`) are the original sources for the data; they are not read by the page.

- **Data**: `BUS_SCHEDULES` and `FGC_SCHEDULES` are huge one-line JSON array literals transcribed from the PDFs. Fields: `dir` (`out` = towards Barcelona, `back` = towards Igualada), `day`, `from`/`to` (`H:MM`), `kind`, `notes`, plus `dubious` (bus entries excluded entirely) and `overnight` (FGC). Edit with care: lines are very long, so prefer targeted replacements over viewing whole lines.
- **Day categories**: `categoryForDate` maps a date to `weekday | saturday | sunday | bcn_local`. Holidays are a manual user toggle (`festiu`), with `KNOWN_HOLIDAYS_2026` / `KNOWN_BCN_LOCAL_2026` used only to warn. Calendar is 2026-specific.
- **Availability rules**: `busAvailable` encodes Monbus legend footnotes as `notes` markers like `(1)`, `(2)`, `(3)`, `(5)`, `(7)`, `(8)` (August, end of December, Easter/Holy Week, school-term days, express `e5` service). Adding or changing a note means updating this function. FGC filtering is inline in `tripsOn` (`weekday`, `aug_weekday`, `weekend`, `common`).
- **Query/render flow**: `tripsOn` (candidates for a date) → `queryTrips` (departure/arrival mode and time filter) → `enrich` (display fields) → `render`/`refresh` (DOM). `setDirection` and `stepDate` handle UI controls.
- Dates are handled as ISO strings, with `Date` objects built at noon (`dateObj`) to avoid timezone/DST shifts.

## Conventions

UI text and comments are in Catalan; keep new user-facing strings in Catalan. FGC night services with replacement buses at Igualada are intentionally not shown.
