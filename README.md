# Lab 1: Requirements Engineering & UML Use-Case Modelling

**Name:** Murali R
**SRN:** PES2UG25AM805
**Section:** CSE (AIML) 'C'

## Problem Statement #59 — Community Lost & Found Matching Network

A lost-and-found community network that extracts visual and text item tags, runs geo-proximity matching between lost and found items, and requires proof-of-ownership question verification before contact details are revealed.

**Target Stakeholders / Actors:** Finder / Owner, Community Admin

---

## 1. Complete Requirements Table

### Functional Requirements (FR-001 to FR-005)

| ID | Type | Description | Priority | Acceptance Criteria | Rationale |
|---|---|---|---|---|---|
| FR-001 | Functional | The system shall match newly logged 'Found Item' posts with active 'Lost Item' reports based on category, color tags, and location radius. | High | **Pass:** Match probability calculated and notification dispatched to potential owner. **Fail:** Incompatible item categories matched. | Core functionality enabling automated reconnection of items to owners without manual searching. |
| FR-002 | Functional | The system shall automatically extract visual and text tags (item category, color, brand, keywords) from the photo and description submitted during a Lost or Found item report. | High | **Pass:** At least 3 relevant tags generated within 5 seconds of upload. **Fail:** No tags generated, or irrelevant tags assigned outside the item's category. | Automated tagging reduces manual data-entry effort and improves downstream matching accuracy. |
| FR-003 | Functional | The system shall require a claimant (owner) to correctly answer the proof-of-ownership questions set by the finder before the finder's handover contact details are released. | High | **Pass:** Contact details are revealed only after all security questions are answered correctly. **Fail:** Contact details revealed after an incorrect answer or without verification. | Protects finders' privacy and prevents fraudulent or false claims on found items. |
| FR-004 | Functional | The system shall allow the Community Admin to review, approve, flag, or remove lost/found item reports that violate community guidelines. | Medium | **Pass:** Flagged report status updated and the reporting user notified within 24 hours. **Fail:** Report remains publicly visible after an admin removal action. | Maintains content quality, safety, and user trust within the community network. |
| FR-005 | Functional | The system shall send a real-time notification to a potential owner whenever a matching found item is identified for their lost item report. | Medium | **Pass:** Notification delivered to the registered device/email within 2 minutes of match calculation. **Fail:** Notification not delivered, or delivered to the wrong user. | Timely notification increases the likelihood of a successful, prompt item recovery. |

### Non-Functional Requirements (NFR-001 & NFR-002)

| ID | Type | Description | Priority | Acceptance Criteria | Rationale |
|---|---|---|---|---|---|
| NFR-001 | Performance & Security | Claimants must answer private security questions set by the finder before item handover contact details are revealed. | High | **Pass:** Benchmarking tests confirm target latency and security standards are met under simulated peak load. **Fail:** Contact details exposed before verification, or latency exceeds target under load. | Ensures the ownership-verification step is both secure against spoofing and fast enough not to discourage genuine claimants. |
| NFR-002 | Usability & Reliability | The system shall maintain 99.5% uptime and allow a user to complete a lost/found report (photo upload + tagging) within 3 minutes on a standard mobile connection. | Medium | **Pass:** Availability logs show ≥99.5% uptime monthly and average submission time ≤3 minutes in user testing. **Fail:** Uptime falls below threshold, or average submission time exceeds 3 minutes. | High availability and a low-friction reporting flow are essential for community adoption, especially during time-sensitive searches. |

---

## 2. UML Use-Case Diagram

See [`usecase_diagram.drawio`](./usecase_diagram.drawio) (editable, open in [diagrams.net](https://app.diagrams.net)) or [`usecase_diagram.png`](./usecase_diagram.png) / [`usecase_diagram.svg`](./usecase_diagram.svg) for the rendered version.

**Actors:** Finder, Owner / Claimant, Community Admin

**Key relationships modelled:**
- `«include»` — Report Lost Item / Log Found Item → Extract Visual & Text Item Tags
- `«include»` — Extract Visual & Text Item Tags → Run Geo-Proximity Matching → Notify Potential Owner
- `«include»` — Claim Found Item → Answer Proof-of-Ownership Questions
- `«extend»` — Reveal Handover Contact Details extends Claim Found Item (Extension Point: `OwnershipVerified`)

---

## 3. Use-Case Flow Specification

| Field | Detail |
|---|---|
| **Use Case ID** | UC-06 |
| **Use Case Name** | Claim Found Item (Proof-of-Ownership Verification) |
| **Primary Actor** | Owner / Claimant |
| **Secondary Actors** | Finder, System (Notification Service) |
| **Trigger** | Owner selects 'Claim This Item' on a found-item post that matches their lost-item report. |

### Brief Description
This use case describes how a registered Owner, notified of a potential match for their lost item, initiates a claim on a found item and is verified as the rightful owner by correctly answering the proof-of-ownership questions set by the Finder, before the Finder's handover contact details are revealed.

### Preconditions
1. The Owner is a registered, logged-in user of the platform.
2. A 'Found Item' post exists whose auto-generated tags and location have already been matched (by UC 'Run Geo-Proximity Matching') against the Owner's active 'Lost Item' report.
3. The Finder has set at least one proof-of-ownership security question while logging the found item.
4. The Owner has received a match notification referencing the found-item post.

### Postconditions
- **Success:** The claim is marked 'Verified', the Finder's handover contact details are released to the Owner, both parties are notified, and the item post status changes to 'Pending Handover'.
- **Failure:** The claim attempt is logged, the attempt counter is incremented, and the Finder's contact details remain hidden.

### Main Success Scenario
1. Owner opens the matched found-item post and selects 'Claim This Item'.
2. System displays the proof-of-ownership question(s) configured by the Finder, without revealing any answer hints.
3. Owner submits answers to all displayed questions.
4. System validates the submitted answers against the Finder's stored answers.
5. System marks the claim as 'Verified' and updates the found-item post status to 'Pending Handover'.
6. System reveals the Finder's handover contact details (phone/chat) to the Owner (extension: Reveal Handover Contact Details).
7. System notifies the Finder that their item has been claimed and verified.
8. Owner and Finder coordinate handover outside the platform using the revealed contact details.

### Alternate Flow A1 — Incorrect Verification Answers
1. At step 4 of the main flow, the System determines that one or more submitted answers do not match the Finder's stored answers.
2. System displays a generic 'Verification failed' message without indicating which specific answer was incorrect.
3. System increments the Owner's failed-attempt counter for this item claim.
4. If the failed-attempt counter is below the allowed limit (3 attempts), the System returns the Owner to step 2 to retry.
5. If the limit is reached, the System locks further claim attempts on this item by the Owner for 24 hours and flags the claim for Community Admin review.
6. Use case resumes at step 1.

---

## Repository Contents

| File | Description |
|---|---|
| `README.md` | This file — full requirements table, use-case diagram summary, and flow specification |
| `usecase_diagram.drawio` | Editable UML use-case diagram (diagrams.net format) |
| `usecase_diagram.png` / `.svg` | Rendered use-case diagram |
| `requirements_table.docx` | Requirements table (Word format) |
| `usecase_flow_specification.docx` | Use-case flow specification (Word format) |
