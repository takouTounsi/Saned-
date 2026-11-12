# Architecture — exploratory state

## Context and actors

Inclusa aims to help beneficiaries prepare social assistance applications and social workers review them. The roles of MAS, UNDP, and the World Bank in the system are `TBD`. No system boundary has been accepted.

## Potential high-level flow

A future journey could connect a beneficiary, preparation assistance, and human validation by a social worker. Any exchange with official references or MAS systems depends on agreements, sources, and interfaces that remain `TBD`. This is not a deployment design.

## Candidate components

Conversational assistant; knowledge and rules engine; document intelligence; case management; authentication; document storage; and social worker interface. These are **functional candidates**, not approved services, repositories, APIs, or team boundaries. OCR, RAG, and eligibility assistance remain options to assess.

## Non-functional constraints

- **Targeted AI:** assess lightweight, explainable approaches suited to each task.
- **Openness and hosting:** favor open solutions and assess local or on-premise hosting; specific choices are `TBD`.
- **Interoperability:** identify existing systems and exchange rules before integration.
- **Accessibility:** test journeys with relevant users; applicable standards and levels are `TBD`.
- **Security and privacy:** define data minimization, role-based access, auditability, and environment separation.
- **Human decisions:** automated results do not replace institutional review or validation.

## Privacy and documents

Applications may contain personal data and sensitive documents. Real beneficiary data and documents must not enter the repository or tests. Retention, encryption, logging, deletion, and access rules must be agreed with responsible stakeholders before implementation (`TBD`).

## Deployment and integration

Hosting mode, environments, identity management, MAS systems, and exchange contracts are `TBD`. No cloud service, data schema, or API contract has been selected.

## Architecture questions

See the [open questions](product/open-questions.md). Every structural choice should be proposed in an [ADR](decisions/README.md), reviewed, and marked **Accepted** by decision-makers before it is treated as a decision.
