# Kiinteistöpito

<p align="center">
  <img src="assets/app-icon.png" width="128" alt="Kiinteistöpito app icon">
</p>

<p align="center">
  A local-first property maintenance logbook built with Flutter.<br>
  It turns scattered records, receipts and old paper documents into a searchable history of a home.
</p>

> This is a public portfolio repository. The application source, configuration and private development material remain in a separate private repository.

## The problem

Homeowners often inherit a fragmented property history. Renovations live in email, invoices sit in folders, maintenance tasks are remembered informally, and older work may only exist on paper. When a problem appears or ownership changes, answering basic questions can take hours: What was repaired, who did it, what did it cost, and where is the document?

## The solution

Kiinteistöpito brings the property lifecycle into one mobile app. It stores the user's data locally by default, records work on a chronological timeline, accepts photos and documents, and performs OCR on the device so scanned text becomes searchable. Property structures, reminders, appliances, recurring costs and long-term maintenance planning share the same property-scoped data model.

The product is designed around a free local-first experience. Production cloud sharing and subscription billing are planned premium services and are clearly separated from what currently works offline.

## Product tour

All screenshots below are captures from the Android emulator using fictional demo data bundled with the app.

### Configurable property dashboard

The card layout combines immediate tasks with structure condition, reminders, waste collection and recurring costs. The same content can also be shown as a compact catalogue or a two-column section dashboard.

<table>
  <tr>
    <td><img src="assets/screenshots/home-01.png" alt="Property dashboard with tasks and structures"></td>
    <td><img src="assets/screenshots/home-02.png" alt="Structure status and maintenance planning"></td>
    <td><img src="assets/screenshots/home-03.png" alt="Reminders, waste collection and costs"></td>
  </tr>
</table>

### Searchable OCR document archive

Images and PDF pages are processed locally. Recognised text is indexed for search and stays linked to the original timeline entry.

<p align="center">
  <img src="assets/screenshots/ocr-documents.png" width="420" alt="Searchable documents with completed OCR">
</p>

### Property history on a timeline

Entries can include an exact or approximate date, performer, contractor, cost, warranty, tax-deduction status, quality rating, notes and attachments.

<p align="center">
  <img src="assets/screenshots/timeline.png" width="420" alt="Chronological property maintenance timeline">
</p>

## Main features

- Local multi-property management with property-scoped records
- Renovation and maintenance timeline with rich structured details
- Camera, gallery, PDF and general file attachments
- Offline image and scanned-PDF OCR with searchable text
- Reviewable OCR suggestions for work-entry fields
- Year-only, month/year and exact dates, each with an optional estimated flag
- Building structures, condition states and editable service-life estimates
- Rule-based 10-year maintenance planning and PDF property reports
- Tasks, recurring maintenance reminders and local notifications
- Appliances, warranties, waste schedules and recurring property costs
- Completed-task recovery and a 30-day trash for deleted records
- Company-ready text export with user-selected property and history fields
- Complete ZIP backup and restore, with optional password encryption
- Finnish and English UI, light and dark themes, scalable text
- Card, compact-list and two-column dashboard layouts

## Typical workflow

```mermaid
flowchart LR
    A[Create or select a property] --> B[Add work, tasks, structures and appliances]
    B --> C[Attach a photo, PDF or scanned paper]
    C --> D[On-device OCR]
    D --> E[Searchable document and timeline record]
    E --> F[Reminders, reports and maintenance planning]
    F --> G[Encrypted backup or reviewed company export]

    H[Future cloud subscription] -.-> I[Household sharing and sync]
    E -. optional later .-> H
```

## Engineering approach

| Area | Approach |
|---|---|
| Client | Flutter and Dart for an Android/iOS codebase, with a web preview fallback |
| State | A property-scoped application model serialised as versioned JSON snapshots |
| Persistence | Durable local snapshot generations, app-owned attachment files and SQLite FTS indexing |
| OCR | Google ML Kit text recognition performed on the device, including scanned PDF pages |
| Documents | `pdfrx` for PDF handling and the `pdf` package for generated reports |
| Recovery | Validated ZIP import/export, checksums, restore checkpoints and optional password encryption |
| Native services | File and image pickers, notifications, secure storage, ads and in-app purchase adapters |
| Quality | Unit, widget, layout, persistence, native integration and release-build checks |

## Technical challenges

### Making OCR survive an optimised release build

OCR worked during development but initially failed after Android code shrinking. ML Kit loads parts of its implementation dynamically, so the release optimiser removed classes that appeared unused. Targeted R8 keep rules were added for ML Kit and Firebase component registrars. The final optimised APK was then tested with a real image, Finnish text recognition and document search.

### Restoring all data safely

A useful backup must contain more than JSON. The restore flow validates archive names, sizes, checksums, attachment presence and the incoming state before changing live data. It uses a restore checkpoint so a failure cannot leave the property database half replaced. Optional encryption protects exported archives with a user password.

### Representing incomplete property history honestly

Many homeowners remember only a year or month. The date model therefore stores precision and uncertainty explicitly instead of inventing a day. The same information survives backups, appears on the timeline and is included in reviewed exports.

### Keeping properties isolated

Tasks, files, OCR jobs, timeline entries and reports carry a property identifier. Tests cover switching homes while OCR is active, duplicate identifiers during restore and cross-property export filtering.

### Supporting dense information on a phone

The interface provides multiple layouts rather than forcing one density. Layout tests cover Finnish and English, light and dark themes, narrow devices and enlarged accessibility text. Complex forms use an adaptive panel that becomes a bottom sheet on phones and a dialog on wider screens.

## Development status

### Implemented and exercised locally

- Local-first multi-property data model and switching
- Timeline, structures, appliances, costs, tasks and reminders
- Attachments, on-device OCR, document viewing and full-text search
- Partial and approximate dates
- Complete and encrypted backup/restore
- Maintenance-plan records, rule-based suggestions and PDF property reports
- Company information export with explicit field selection
- Local notification scheduling
- Responsive layouts, translations, themes and accessibility checks

### Implemented as integration groundwork, not production-verified

- Supabase account, invitation, role, quota and synchronisation clients
- Cloud entitlement handling and subscription plan UI
- Android purchase-verification service source
- AdMob and in-app-purchase adapters using non-production configuration
- Waste-provider abstraction

### Future work and release dependencies

- Deploy and test the production cloud backend, storage policies and account lifecycle
- Configure real store products, server verification, production signing and purchase restoration
- Replace test advertising configuration or ship with advertising disabled
- Complete live Google Play and App Store acceptance testing
- Connect and license a live waste-collection data provider
- Add household sharing only after production security and privacy review
- Consider a paid AI-assisted maintenance planner; the current planner is deterministic

No public release, production subscription, live cloud sync or live waste-provider integration is claimed in this portfolio.

## My contribution and use of AI

I defined the product direction, prioritised the release scope, specified user flows, reviewed UI iterations, tested builds on Android devices and decided which features belonged in the first release. I also directed the privacy, licensing, monetisation and competitor research that shaped the implementation.

Development was AI-assisted. Codex coding agents helped implement and refactor Flutter code, generate tests, investigate release failures, audit dependencies and document decisions. I reviewed the resulting behaviour through tests, emulator sessions and device builds, and iterated on issues found during use. This repository presents that collaboration accurately rather than describing the project as entirely hand-written.

## Case study

The concise project case study covers the design decisions, difficult engineering work and remaining release risks:

**[Read the case study](docs/case-study.md)**

## CV description

> Designed and developed an AI-assisted, local-first Flutter application for property lifecycle management. Implemented multi-property records, on-device OCR and full-text document search, partial-date modelling, encrypted full-data backups, configurable mobile layouts, maintenance planning and PDF exports. Validated the product through automated tests, optimised Android builds and emulator/device testing while keeping production cloud and billing work explicitly separated from completed features.

## Repository scope

This repository contains only reviewed portfolio documentation and presentation assets. It intentionally excludes application source code, credentials, configuration, logs, build artifacts, private screenshots and development transcripts.

Copyright © 2026 Visa. All rights reserved.
