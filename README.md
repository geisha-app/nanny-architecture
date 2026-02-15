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
  devices across Apple, web, and Android ecosystems. On-device cry detection,
  audio passthrough to headphones, video streaming between devices, and
  wearable companion alerts. Three parallel development streams converge through
  a shared signalling protocol for cross-ecosystem interoperability. See
  [Nanny Monitor architecture](#nanny-monitor-architecture) below.

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

## Nanny Monitor architecture

Nanny Monitor develops across three parallel streams — Apple, web, and
Android — each exploiting its ecosystem's unique capabilities while converging
through a shared protocol for cross-platform interoperability.

### Why three streams

The web alone cannot reliably deliver a baby monitor. Background execution is
unreliable — iOS Safari suspends tabs within seconds of screen lock. Hardware
integration (AirPods spatial audio, Apple Watch haptic alerts, Wear OS
complications, CarPlay dashboards) is inaccessible from a browser. Audio
routing lacks device-level control. Always-on reliability requires native
audio session configuration that signals to the operating system that this
audio stream must not be interrupted.

Each stream exists because it solves problems the others cannot. The value
comes from their ability to interoperate.

### Progressive enhancement

Every stream follows the same conceptual progression, where each tier delivers
genuine standalone value:

```
Tier 0 — Single device, self-contained
Tier 1 — Two devices, local network, no server
Tier 2 — Wearable companion (alerts and control)
Tier 3 — Persistent UI surface (lock screen, complication, tile)
Tier 4 — Living room and vehicle integration
Tier 5 — Cross-ecosystem interop via shared protocol
Tier 6 — Multi-caregiver, multi-viewer
```

A person who never moves past tier 0 still has a useful baby monitor. A person
who reaches tier 5 can have an Android baby station streaming to an iPad parent
station with an Apple Watch receiving haptic alerts — all because the interop
protocol ties the streams together.

### The Nanny signalling protocol

The three streams converge through a shared signalling and streaming protocol.
Nanny uses WebRTC as the media transport and defines a thin signalling layer
on top of WebSocket that all three streams speak.

The Nanny signalling protocol defines:

- **Session advertisement** — a baby station announces itself with a device
  capability manifest (audio-only versus audio and video, supported codecs,
  ML models available, battery level, device name)
- **Session negotiation** — standard SDP offer/answer exchange tunnelled
  through WebSocket JSON messages
- **ICE candidate relay** — standard trickle ICE, tunnelled identically
- **Alert propagation** — when any station's ML model detects an event (cry,
  motion, noise threshold), it broadcasts a structured alert to all connected
  parent stations
- **Control messages** — parent stations can adjust baby station sensitivity,
  toggle video, request audio level data, or mute passthrough
- **Presence** — which caregivers are connected, which baby stations are active,
  connection health

The signalling server is intentionally minimal — it relays messages between
peers and maintains no media state. It can be self-hosted as a single binary.
For local-network scenarios, the signalling server is unnecessary; each
platform uses its native peer-to-peer discovery.

### Codec alignment

| Media | Required | Preferred | Rationale |
|-------|----------|-----------|-----------|
| Audio | Opus | — | Supported natively by WebRTC on all platforms. Low latency, excellent quality at low bitrate |
| Video | H.264 Baseline | H.265/HEVC | H.264 is hardware-accelerated everywhere. H.265 offers better quality at lower bitrate on Apple and most Android devices, with H.264 fallback for web |

### ML model alignment

Each stream uses its platform's native ML framework for cry detection. All are
trained from the same dataset and produce alerts in the same JSON format. The
model itself does not need to be identical across platforms — what matters is
that the alert semantics are consistent.

The training pipeline produces:
- Apple: Core ML model alongside the built-in SoundAnalysis classifier
- Android: TensorFlow Lite model optimised for hardware acceleration
- Web: ONNX model compiled to WebAssembly

### Cross-stream interoperability

| Baby station | Parent station | Connection method | Available from |
|-------------|---------------|-------------------|----------------|
| Apple | Apple | Native peer-to-peer | Tier 1 |
| Apple | Apple Watch | Native companion | Tier 2 |
| Apple | Web | Signalling protocol + WebRTC | Tier 5 |
| Apple | Android | Signalling protocol + WebRTC | Tier 5 |
| Android | Android | Native peer-to-peer | Tier 1 |
| Android | Wear OS | Native companion | Tier 2 |
| Android | Web | Signalling protocol + WebRTC | Tier 5 |
| Android | Apple | Signalling protocol + WebRTC | Tier 5 |
| Web | Web | WebRTC peer-to-peer | Tier 1 |
| Web | Apple | Signalling protocol + WebRTC | Tier 5 |
| Web | Android | Signalling protocol + WebRTC | Tier 5 |

Within-ecosystem connections use native protocols for superior performance and
reliability. Cross-ecosystem connections use the signalling protocol and
WebRTC as the universal bridge.

### Shared components

- **Cry detection model** — single training pipeline producing platform-native
  models with consistent sensitivity presets and alert semantics
- **Alert format** — structured JSON with device information, event type,
  confidence, sensitivity preset, audio level, and model metadata
- **Signalling server** — single implementation serving all three streams,
  speaking the Nanny signalling protocol over WebSocket
- **Authentication** — all streams authenticate through `auth.geisha.app` for
  server-mediated connections; local connections require no authentication

### Privacy at every tier

Tier 0 requires no network. Tiers 1 and 2 require no internet. Tiers 3 through
5 require a signalling server that sees no media. At no point does audio or
video pass through any infrastructure unless the person explicitly chooses to
use the hosted signalling service instead of self-hosting.

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

**Nanny Monitor — Apple stream**
- Single-device audio monitoring with cry detection and headphone passthrough
- Two-device local monitoring via native peer-to-peer with video
- Apple Watch companion with haptic alerts, complications, and glanceable UI
- Live Activities and Dynamic Island for persistent monitoring status
- Apple TV nursery dashboard and CarPlay vehicle monitoring
- WebRTC interop bridge for cross-ecosystem connections
- SharePlay multi-viewer, HomePod baby station, Siri Shortcuts

**Nanny Monitor — web stream**
- Single-tab audio monitoring with Wasm-based cry detection
- WebRTC peer-to-peer two-browser monitoring with QR code pairing
- Progressive web application with Service Worker and push notifications
- Signalling server integration for remote access
- Advanced ML pipeline (motion detection, noise classification)
- Full signalling protocol interop and multi-viewer with optional recording

**Nanny Monitor — Android stream**
- Single-device monitoring with foreground service and cry detection
- Two-device local monitoring via native peer-to-peer with video
- Wear OS companion with haptic alerts, tiles, and complications
- Notification channel hierarchy and home screen widgets
- Android TV nursery dashboard and Android Auto vehicle monitoring
- WebRTC interop bridge for cross-ecosystem connections
- Google Home integration and multi-viewer

**Nanny Monitor — shared components**
- Nanny signalling protocol specification
- Alert format specification
- ML model training pipeline producing platform-native models
- Signalling server (self-hostable)
- Codec alignment and interoperability testing

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
- Minttu core CSS, Geisha style library, Nanny style
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
