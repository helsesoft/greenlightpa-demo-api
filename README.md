GreenLight PA — EHR Integration Demo
Helsesoft LLC | greenlightpa.com

What This Is
This repository demonstrates how GreenLight PA can function as a real-time prior authorization intelligence layer within an EHR workflow.

GreenLight PA normalizes insurer PA criteria for dermatology biologics — including step therapy requirements, quantity limits, and documentation requirements — and exposes them via a structured API. The intended integration point is upstream of order signing: when a provider selects a medication, the patient's insurer is resolved from the chart, and the relevant PA criteria surface automatically before the order is placed.

This demo is scoped to a single policy combination:

Drug	Insurer	State	Indication
Dupixent (dupilumab)	Anthem BCBS	CA	Atopic Dermatitis
Repository Contents
File	Description
dupixent-anthem-ca.json	Normalized PA criteria for Dupixent / Anthem BCBS / CA
index.html	Interactive EHR integration demo — fetches and renders the JSON
Running the Demo
Because index.html fetches dupixent-anthem-ca.json via a relative path, it requires a simple local server to run correctly (browsers block fetch() on file:// URLs).

Option 1 — Python (no install required):

cd greenlight-pa-demo
python3 -m http.server 8080
# Open: http://localhost:8080

Option 2 — Node.js: npx serve .

Option 3 — GitHub Pages:

The demo is hosted directly from this repository at:
https://postweb21.github.io/greenlightpa_demo_api/](https://helsesoft.github.io/greenlightpa-demo-api/


JSON API Structure
The data file is a self-contained JSON object. A production API would accept query parameters (drug, insurer, state, indication) and return the same structure. The key fields:

policy             → Drug, insurer, state, policy reference, source URL
criteria
  initial_authorization   → Typed, ordered requirements (age, diagnosis, step therapy)
  continuation_authorization → Clinical response requirements
quantity_limits    → Formulation- and age-specific schedules; loading dose override
exclusions         → Concurrent use restrictions
documentation_checklist → Renderable checklist for PA submission
Each criterion carries an id field (IA-1, IA-2, etc.) to support field mapping to an EHR's own data model.

A note on authorization duration: The Anthem BCBS CC-0029 policy does not specify an authorization duration for the atopic dermatitis indication. This is reflected accurately in the data. Clinical judgment and insurer renewal cadence apply at continuation.

Integration Concept
In an EHR-integrated implementation:

Provider selects medication (e.g., Dupixent) at order entry
EHR resolves insurer from chart (plan name, state)
GreenLight PA API is queried with drug + insurer + state
Criteria surface in a sidebar panel before the order is signed
Documentation checklist guides the PA submission directly from the encounter
No PHI is collected or transmitted by the API. Insurer and drug identifiers are the only inputs.

The UI chrome in index.html is intentionally generic. Field mapping to a specific EHR's chart data model would be straightforward given the structured JSON schema.

Coverage
The production GreenLight PA library covers dermatology biologics across multiple states and insurers. This repository exposes a single policy combination for evaluation purposes.

Legal
This repository is provided to authorized recipients for evaluation purposes only. No rights to use, reproduce, distribute, or create derivative works are granted without express written permission from Helsesoft LLC. All rights reserved.

One or more aspects of this technology are subject to pending patent applications.

Contact: support@helsesoft.com
