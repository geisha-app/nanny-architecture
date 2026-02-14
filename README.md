# Geisha Nanny architecture

Welcome! This repository is where architectural decisions for
[Geisha Nanny](https://gitlab.com/geisha.app/nanny) are proposed, discussed,
and recorded.

## What is Nanny?

Nanny is a privacy-first baby care tracking platform. It gives families complete
ownership of their child's developmental data through local-first storage,
service-agnostic sync, and zero-knowledge encryption — while providing
evidence-based insights powered by on-device intelligence.

The name reflects its purpose: a nanny is a trusted expert who supports families
with attentive, knowledgeable care. Nanny tracks sleep, feeding, growth, and
developmental milestones with the same careful attention, helping parents and
caregivers make informed decisions backed by clinical standards (CDC, WHO, AAP).

Nanny works immediately without account creation. Data stays on the device by
default. When families choose to sync, they pick where their data lives —
hosted, self-hosted, or distributed — with end-to-end encryption throughout.

## Sub-projects

Nanny encompasses three related applications:

- **Nanny** (core) — baby care tracking covering sleep sessions, feeding
  (breast, bottle, solids), diaper changes, growth measurements, medication,
  and developmental milestone tracking with CDC 2022 integration. Multi-caregiver
  collaboration with role-based permissions for parents, grandparents,
  babysitters, and healthcare providers.

- **Nanny Monitor** — smartphone-based baby monitoring that repurposes existing
  devices. Microphone-based sound analysis infers sleep state (sleeping,
  stirring, awake), with ambient soundscape generation (rain, ocean, wind,
  shush), nightlight control, and activity logging. Local peer-to-peer via
  WebRTC for privacy, with optional server-mediated remote access.

- **Nanny Tempo** — contraction timing and labour guidance for pregnancy. A
  focused progressive web application providing evidence-based contraction
  tracking, clinical protocol research (ACOG, WHO, NHS, RCOG guidelines),
  multi-party sync for both partners, and family notification for hospital
  departure. Data is independent from core Nanny with a migration path
  post-birth.

## Ecosystem context

Nanny operates within the broader Geisha and Naamio ecosystem:

- **Naamio Link Space** provides the federation layer — service discovery via
  `.well-known/naamio-link`, cross-instance data sharing, and encrypted sync
  infrastructure that Nanny consumes as a client.

- **Sinetti** provides identity and authentication via `auth.geisha.app`,
  supporting OAuth 2.1, OpenID Connect, WebAuthn/passkeys, and Biscuit tokens
  across the Geisha suite.

- **Geisha suite** encompasses privacy-first lifestyle applications (Barista,
  Chef, Grocer, Courier, Herald, Host, Guide, Aide), all governed by the Omnifi
  Foundation and built on shared Minttu design language.

## What this repository is for

This repository tracks architectural decisions using two complementary
approaches:

- **Decisions** capture internal technical and organisational choices — how
  Nanny is built, structured, and maintained.

- **Comments** handle community-facing proposals — changes to public interfaces,
  features, behaviour, and integration patterns that affect how people use
  Nanny.

These terms map to well-established practices — decisions are also known as
architecture decision records (ADRs), and comments are also known as requests
for comments (RFCs). We use plainer language to lower the barrier to
contribution.

Both approaches are open to everyone. You don't need to be a maintainer or a
regular contributor to submit a proposal. If you have an idea or see something
that could be improved, you're welcome here.

## How the process works

1. **Create an issue** using one of the issue templates (decision or comment) to
   signal your intent and invite early feedback.
2. **Draft a proposal** using the document templates in `templates/`.
3. **Submit a merge request** with your proposal in `decisions/` or `comments/`.
4. **Discuss** — for decisions, technical leads review over 7–14 days. For
   comments, the community discusses for a minimum of 14 days.
5. **Decision** — once consensus is reached, the proposal is merged and becomes
   part of the project's record.

The full process, including how consensus works, how disagreements are resolved,
and what happens with urgent decisions, is documented in the
[Omnifi Foundation handbook](https://handbook.omnifi.foundation/engineering/architecture/).

## Projects in scope

Proposals in this repository may affect any part of the Nanny ecosystem:

**Core tracking**
- Sleep session tracking with duration, quality, location, and wake reason
- Feeding tracking (breastfeeding with L/R timing, bottle with volume, solids
  with allergen monitoring)
- Diaper change logging with Bristol Stool Chart classification
- Medication tracking with dosage calculations and safety checks
- Growth measurement with WHO/CDC percentile calculations (LMS method)
- Developmental milestone tracking with CDC 2022 checklist integration
- Age-corrected tracking for premature infants

**Nanny Monitor**
- Microphone-based audio monitoring with RMS sound level analysis
- Sleep state inference (sleeping, stirring, awake) from audio patterns
- Ambient soundscape generation (rain, ocean, wind, shush)
- Night light control with brightness and warmth adjustment
- Activity logging with state transition tracking
- Real-time waveform and frequency spectrum visualisation
- Wake Lock API for uninterrupted monitoring
- Local peer-to-peer via WebRTC with mDNS and QR code pairing
- Optional server-mediated remote access with DTLS-SRTP encryption
- Cry detection and motion analysis via on-device machine learning

**Nanny Tempo**
- Contraction timing with start/stop and duration tracking
- Evidence-based hospital decision support (ACOG, WHO, NHS, RCOG)
- Multi-party sync for partner monitoring
- Family notification system for hospital departure
- Clinical protocol research and guidance
- Post-birth data migration to core Nanny

**Service layer**
- Rust service with Wasm plugin extensibility
- FHIR R4 data modelling for healthcare interoperability
- SMART on FHIR for authorised healthcare access
- HL7 CDA clinical document generation
- Pediatrician report generation and well-child visit preparation
- Vaccination schedule tracking
- SNOMED CT and RxNorm medical terminology
- Role-based access control (parent, caregiver, grandparent, babysitter,
  healthcare provider)

**Web application**
- Deno Fresh 2 progressive web application with Islands Architecture
- Declarative shadow DOM and custom web components
- Offline-first via Service Workers and IndexedDB
- Minttu core CSS → Geisha style library → Nanny style
- View Transitions API for smooth navigation
- Web Share Target API for content capture
- Push notifications for reminders and alerts

**Sync and data sovereignty**
- CRDT-based sync engine (Yjs) for conflict-free merging across devices
- Zero-knowledge end-to-end encryption (AES-GCM, client-side)
- WebDAV sync backend (RFC 4918) for self-hosted interoperability
- Multi-backend registration with per-content-type routing
- Privacy dashboard showing data flow across services
- Complete data export (JSON, CSV, PDF, FHIR R4)

**Intelligence**
- On-device pattern recognition for sleep, feeding, and growth trends
- Sleep prediction modelling based on circadian rhythm research
- Anomaly detection for health concerns
- Content relevance scoring based on developmental stage
- Wasm plugin interface for custom analysis and enrichment
- Privacy-preserving community insights via differential privacy

**Community and collaboration**
- Multi-caregiver real-time sync with conflict resolution
- Role-based permissions with fine-grained access control
- Caregiver handoff notes and care scheduling
- Shared care timelines across family members
- Federation via Naamio Link Space for cross-instance data sharing

If your proposal spans multiple areas, note all affected projects in your
proposal so the right people can weigh in.

## Governance

Geisha Nanny is governed by the
[Omnifi Foundation](https://omnifi.foundation), a community-driven organisation
that stewards open source projects. The architecture decision process — how
proposals are written, reviewed, and decided — is defined in the
[Omnifi Foundation handbook](https://handbook.omnifi.foundation/engineering/architecture/)
and applies equally to all contributors.

Decisions are made through consensus. Technical leads facilitate the process but
don't dictate outcomes. Every voice carries weight, and dissenting perspectives
are documented and valued. See the
[governance model](https://handbook.omnifi.foundation/engineering/architecture/governance/)
for full details.

## Getting started

New to the project? Here's how to get oriented:

1. **Browse existing proposals** in `decisions/` and `comments/` to see what's
   been decided and how proposals are structured.
2. **Check open merge requests** for proposals currently under discussion.
3. **Read the handbook** for
   [detailed process guidance](https://handbook.omnifi.foundation/engineering/architecture/).
4. **Open an issue** if you have questions — there are no bad questions.

## Repository structure

```
├── README.md              You are here
├── CONTRIBUTING.md        How to submit proposals
├── templates/
│   ├── decision.md        Decision template
│   └── comment.md         Comment template
├── decisions/             Accepted decisions
├── comments/              Accepted comments
└── .gitlab/
    └── issue_templates/
        ├── decision.md    Issue template for proposing a decision
        └── comment.md     Issue template for proposing a comment
```

## Code of conduct

All participation is subject to the
[Omnifi Foundation code of conduct](https://handbook.omnifi.foundation/CODE_OF_CONDUCT/).
We're committed to a welcoming, respectful, and inclusive environment.

## License

CC BY-SA 4.0 — see LICENSE for details.
