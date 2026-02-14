---
name: Decision
about: Propose a technical or organisational decision
labels: decision, architecture
---

# Decision

<!--
Use this template to propose technical and organisational decisions.
For community-facing changes and feature proposals, use the comment template
instead.

Decisions are also known as architecture decision records (ADRs).

After creating this issue, draft your full proposal using the template at
templates/decision.md and submit a merge request.

Process details: https://handbook.omnifi.foundation/engineering/architecture/governance/
-->

## Overview

### Title
<!-- A clear, descriptive title for the decision -->

### Affected components
<!-- Which components does this decision affect? -->
- [ ] Web application (interactive components, progressive web application)
- [ ] Service (Rust service layer, Wasm plugins, healthcare integration)
- [ ] Data (models, storage, local-first data)
- [ ] Sync (synchronisation, offline-first, conflict resolution)
- [ ] Style (CSS, design system, Minttu core)
- [ ] Monitor (audio monitoring, soundscape, nightlight)
- [ ] Tempo (contraction timing, labour guidance)
- [ ] Infrastructure (deployment, hosting, edge computing)
- [ ] Specifications (OpenAPI, Protocol Buffers, WebAssembly Interface Types)
- [ ] Other: <!-- specify -->

---

## Problem statement

### Current situation
<!-- Describe the technical or organisational situation requiring this decision -->

### Decision drivers
- <!-- Driver 1 -->
- <!-- Driver 2 -->

---

## Proposed decision

### Chosen approach
<!-- State the proposed decision clearly -->

### Rationale
<!-- Why is this approach being proposed? -->

### Alternatives considered
<!-- Briefly list other approaches you considered -->

---

## Impact summary

### Technical impact
<!-- How does this affect the codebase and architecture? -->

### Contributor impact
<!-- How does this affect how people contribute to the project? -->

---

## Next steps

- [ ] Draft full proposal in `decisions/XXXX-title.md`
- [ ] Submit merge request for review
- [ ] Address feedback from technical leads
- [ ] Update status after decision

---

## Governance

This decision follows the
[Omnifi Foundation governance model](https://handbook.omnifi.foundation/engineering/architecture/governance/).
See the [handbook](https://handbook.omnifi.foundation/engineering/architecture/)
for process details.

