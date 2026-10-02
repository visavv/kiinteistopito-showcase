# Case study: Kiinteistöpito

## Context

Kiinteistöpito began from a practical problem: a house can have decades of repairs, inspections and maintenance, but no single reliable history. Existing information is commonly split between memory, paper documents, photographs, email and contractor invoices. The loss becomes visible when a fault needs diagnosis, a recurring task is missed, or the property is sold.

The project explores how a mobile application can become a durable property logbook without requiring an account or cloud subscription for basic use.

## Product principles

1. **Useful before registration.** The local application must work without an account.
2. **Evidence stays attached to the event.** A renovation record should carry its documents, costs and contractor information.
3. **Uncertainty must be represented.** A remembered year is valid information and must not be converted into a fictional exact date.
4. **Search should include paper history.** OCR is part of the core record flow rather than a separate scanning utility.
5. **Cloud features must earn their operational cost.** Sharing and synchronisation are planned subscription benefits, while local use remains available without them.

## Implemented experience

The app organises data by property. Each property combines:

- a maintenance and renovation timeline;
- building structures with condition and service-life information;
- tasks and recurring reminders;
- appliances and warranties;
- waste schedules and recurring costs;
- documents, photographs and other files;
- long-term maintenance-plan entries;
- backup, property-report and company-export workflows.

Users can scan or import historical documents. The OCR queue processes images and PDF pages on the device, stores recognised text with the attachment and indexes that text for search. A scanned building permit can therefore be found later by a word inside the document.

## Key engineering decisions

### Local-first storage

The app serialises its domain state as a versioned JSON snapshot while keeping attachments as app-owned files. Durable snapshot generations and a restore checkpoint reduce the risk of losing the current state during interrupted writes or imports. SQLite full-text search provides a local index for OCR content.

This architecture keeps the offline product simple and supports complete exports. It also means future cloud synchronisation must reconcile structured records and binary files rather than treating the phone as a thin client.

### OCR as an asynchronous application service

OCR is managed by an application-owned queue. Jobs remain associated with the correct property and attachment even if the user switches homes. Status and errors are visible, and results are persisted page by page. Image recognition uses ML Kit locally. PDF extraction handles both text PDFs and scanned pages.

Release testing exposed an Android optimiser problem: reflective ML Kit classes were removed by R8. Narrow keep rules fixed the release build without disabling optimisation globally.

### Full backup instead of partial export

The backup format includes the application snapshot and attachment bytes. Import checks archive traversal, duplicate identifiers, missing files, size limits and checksums before applying the state. Optional password encryption protects a portable backup.

A separate company export intentionally contains less information. The user chooses the property fields, appliances, history entries and OCR text to include, reviews the generated text and then selects the recipient through the operating system.

### Historical dates with explicit precision

The date model supports year, month/year and full-date values. Each can be marked as estimated. Forms, JSON round trips, timeline presentation and exports preserve that distinction.

### Responsive information density

Property management creates dense screens. The home dashboard supports cards, a compact catalogue and a two-column section overview. The timeline supports detailed cards and compact expandable rows. Layout checks cover two languages, themes, text scaling and narrow screens.

## AI-assisted development process

The project owner provided the product vision, requirements, priorities, acceptance feedback and hands-on device testing. Codex agents assisted with coding, refactoring, automated tests, audits, research and documentation. Features were developed through repeated review cycles based on emulator and physical-device screenshots.

This process was especially useful for broad regression coverage and for investigating platform-specific failures such as release-only OCR behaviour. Human review remained necessary for product trade-offs, visual quality, privacy boundaries and deciding which integrations were credible enough to describe as complete.

## Current boundaries

The local product is substantially implemented and has been exercised through automated and Android runtime checks. The following work is deliberately not presented as complete:

- the prepared Supabase cloud stack has not been deployed and accepted in production;
- household invitations and shared cloud properties therefore are not a live customer feature;
- real store subscriptions, purchase verification and production advertising require external configuration and acceptance testing;
- the waste-provider interface has no licensed live provider;
- the 10-year planner uses deterministic service-life rules, not a production AI service;
- public-store release, iOS device testing and production signing remain release work.

## Outcome

Kiinteistöpito demonstrates a coherent mobile domain model across property records, documents, OCR, recovery and reporting. Its strongest technical result is that paper evidence becomes part of a searchable, portable property history while basic use remains local and independent of a cloud account.
