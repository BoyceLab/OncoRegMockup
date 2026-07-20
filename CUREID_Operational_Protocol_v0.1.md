# Operational Protocol: Participant-Directed Oncology Registry to CURE ID

**Document type:** Operational data flow protocol
**Version:** 0.1 (draft for discussion)
**Status:** Not approved. Three items in §6 block execution.
**Scope note:** Covers data flow only. Analysis is governed by a separate protocol (§5).

---

## 1. Purpose

This protocol defines how participant-contributed data moves from enrollment to an attested case report submitted to CURE ID, and what controls apply at each step. It covers a single participant's data through one submission cycle.

**Flow summary:**

> Consent → Acquire (records ‖ surveys) → Normalize → Common data model → Derive → Attest → Disclosure control → Submit ⟳ Feedback

---

## 2. Steps

**Step 0 — Enrollment and consent**
*Actor:* Participant, with registry staff support.
Consent is captured in three separately revocable tiers: (a) registry participation; (b) contribution of derived case reports to CURE ID, named as a recipient; (c) contribution of variant data to GenomeConnect/ClinVar, named as a recipient. Tiers (b) and (c) are independent of each other and of (a).
*Output:* Consent record with tier status and timestamps.

**Step 1a — Record acquisition**
*Actor:* Participant, authorizing directly.
Participant authorizes retrieval from one or more health systems via OAuth 2.0 / SMART on FHIR. Direct C-CDA upload is supported as an alternative and as a supplement. Multiple connections per participant are expected and supported; the count and coverage of connections is recorded as a data-quality attribute.
*Output:* Raw FHIR bundles and C-CDA documents, retained unmodified as the source of record.

**Step 1b — Survey capture**
*Actor:* Participant.
Comprehensive HPO-coded health survey, plus outcome and integrative-therapy instruments. Delivered modularly across sessions with save-and-resume. Completion is recorded per module, not as an aggregate.
*Output:* Coded survey responses with HPO term bindings.

Steps 1a and 1b are independent. Neither blocks the other, and a participant may complete either alone.

**Step 2 — Normalization**
*Actor:* Automated pipeline.
Terminology resolution against RxNorm, SNOMED CT, ICD-10-CM, and LOINC. Every translation child of a coding is walked so that inner codes are not lost inside outer ones. Records from multiple connections are de-duplicated at the event level.
*Output:* Normalized clinical events. Original source codes and text are retained alongside every mapped value.

**Step 3 — Common data model load**
*Actor:* Automated pipeline.
Load to OMOP CDM, which is the registry of record and the substrate for all analysis. Survey-derived phenotype enters the condition domain via HPO → MONDO → standard concept. Survey and record provenance are preserved as distinct at value level.
*Output:* Person-level longitudinal record.

**Step 4 — Case derivation**
*Actor:* Automated pipeline.
Assemble lines of therapy. Produce a first-pass off-label classification by comparing the RxNorm ingredient against labeled indications and against the treated condition, and by comparing dose, route, schedule, and combination against the label. Map outcomes and collapse internal value sets into CRF response options per the submission dictionary.
*Output:* Candidate case, plus a reasoning trace for every derived value.

**Step 5 — Attestation**
*Actor:* Participant, and/or clinician per the model agreed in §6.
The attestor is shown every derived value with its source, corrects any value, supplies the survey-only elements not otherwise captured, and signs. Values the attestor cannot confirm are marked unconfirmed and withheld rather than submitted with an assumed value.
*Output:* Attested case with attestor identity, role, and timestamp.

**Step 6 — Disclosure control**
*Actor:* Automated pipeline, with staff review of free text.
Apply the privacy transform recorded against each element in the data dictionary: age banding, year-and-month-only dates, interval-only durations, suppression below the k threshold, and manual review of every free-text field. Assign a registry-issued case identifier from the agreed namespace.
*Output:* Submission file, and a participant-facing preview of exactly what will be disclosed.

**Step 7 — Submission and feedback**
*Actor:* Registry, to CURE ID (NCATS/FDA).
Transmit via the agreed structured pathway. De-duplicate against prior submissions and, per the agreement in §6, against cases submitted independently by clinicians. Curated signals returned by CURE ID are used to re-query the registry cohort.
*Output:* Submitted case; logged submission record; returned signals.

---

## 3. Control gates

Three hard stops. Each blocks advancement of the affected record, not the pipeline as a whole.

| Gate | Point | Condition |
|---|---|---|
| **G1** | After Step 0 | No data advances past Step 3 toward submission unless consent tier (b) is active. Tier (a) alone permits registry use only. |
| **G2** | End of Step 4 | No candidate case advances without a complete reasoning trace for every derived value. |
| **G3** | End of Step 5 | No value is exported unattested. Unconfirmed values are withheld, never inferred. |

---

## 4. Standing rules

**Provenance.** Every value carries an internal tag: structured record, narrative extraction, survey, computed, or attestor-supplied. Provenance is stored per value instance, not per element, because the same element may be record-derived for one participant and survey-answered for another.

**Off-label classification.** Proposed by software, decided by a human, reasoning stored. No exception.

**Revocation.** Withdrawal of consent tier (b) halts all further submission immediately. Withdrawal of previously submitted cases follows the procedure agreed with the CURE ID team under §6, and the absence of that procedure is itself a blocking item.

**Source retention.** Raw bundles and documents are retained unmodified. Mapping decisions are revisited; discarding source values makes remapping impossible.

---

## 5. Exclusions

This protocol does **not** cover: statistical analysis or causal inference on the registry cohort, which is governed by a separate pre-registered analysis protocol; institution-mediated bulk exports; tokenized third-party linkage. The last two are undecided and would each require an amendment.

---

## 6. Blocking open items

Execution cannot begin until these are resolved with NCATS and FDA.

1. **Attestation model.** Whether a record-derived, participant-confirmed case is submitted under the participant/care-partner reporter branch, the provider branch, or a new reporter type. Several CRF outcome options branch on this.
2. **Case identifier namespace and de-duplication rule.** Required before the first submission, including the treatment of cases already submitted independently by a clinician.
3. **Structured intake pathway.** Whether derived cases can be submitted in structured form, and the withdrawal procedure for a previously submitted case.

Two further items are needed but not blocking: the ICD-10-CM/SNOMED CT to CURE ID disease-list binding (§Step 4), and whether CURE ID can receive a provenance attribute alongside outcome values.

---

*Prepared for internal discussion. Not reviewed or endorsed by FDA, NCATS, or NIH.*
