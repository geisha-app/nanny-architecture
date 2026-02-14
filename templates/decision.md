# Decision

<!--
This template helps create proposals for technical and organisational decisions.
Decisions record internal choices about how the application is built, structured,
and maintained. For community-facing changes, use the comment template instead.

Decisions are also known as architecture decision records (ADRs).

Process details: https://handbook.omnifi.foundation/engineering/architecture/governance/
-->

## Overview

### Title
<!-- A clear, descriptive title for the decision -->

### Number
<!-- Sequential number: 0001, 0002, etc. -->

### Status
<!-- Current status of this decision -->
- [ ] Proposed
- [ ] Accepted
- [ ] Rejected
- [ ] Deprecated
- [ ] Superseded

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
<!-- What factors are influencing this decision? -->
- <!-- Driver 1 -->
- <!-- Driver 2 -->
- <!-- Driver 3 -->

### Constraints
<!-- What constraints must the decision respect? -->

---

## Proposed decision

### Chosen approach
<!-- State the proposed decision clearly -->

### Rationale
<!-- Why is this approach being proposed? -->

### Consequences
<!-- What are the expected consequences? -->

---

## Alternatives considered

### Alternative 1: <!-- Name -->
**Description**: <!-- What this alternative involves -->
**Advantages**: <!-- Advantages -->
**Disadvantages**: <!-- Disadvantages -->
**Decision**: <!-- Why chosen or rejected -->

### Alternative 2: <!-- Name -->
**Description**: <!-- What this alternative involves -->
**Advantages**: <!-- Advantages -->
**Disadvantages**: <!-- Disadvantages -->
**Decision**: <!-- Why chosen or rejected -->

---

## Impact summary

### Technical impact
<!-- How does this affect the codebase and architecture? -->

### Contributor impact
<!-- How does this affect how people contribute to the project? -->

### Security impact
<!-- How does this affect the security posture? -->

---

## Implementation notes

### Approach
<!-- Key implementation steps, migration considerations, testing requirements -->

### Verification
<!-- Success criteria, testing approach, monitoring requirements -->

---

## References

### Related decisions
<!-- Link to related or dependent decisions -->

### External references
<!-- Links to relevant standards, specifications, research, or implementations -->

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
Decisions use lazy consensus — see the
[handbook](https://handbook.omnifi.foundation/engineering/architecture/) for
details.

---

/label ~"decision" ~"architecture"
