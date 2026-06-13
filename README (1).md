# OncoRegistry Mockup

An interactive, non functional mockup of a patient mediated oncology registry. It mirrors a REDCap style
workflow and adds participant authorized electronic health record data through OAuth 2.0 and SMART on FHIR,
the same pattern used by clinical data interoperability services, opened so that any participant can self
direct their records rather than only patients at one connected institution.

Built for demonstration to people who do not build databases. It shows how you design a data dictionary,
what participants see, how an EHR connection works, and how the combined data are visualized and exported.

## What is inside

- **index.html** the whole interactive site, self contained, no build step, no external dependencies.
- **OncoRegistry_DataDictionary.csv** a valid REDCap data dictionary. Import it into a real REDCap project to
  start the survey side immediately.

## Run it locally

Open `index.html` in any browser. That is all.

## Publish on GitHub Pages

1. Create a repository, for example `onco-registry-mockup`.
2. Add `index.html` (and optionally the CSV and this README) to the repository root.
3. In the repository, go to **Settings > Pages**.
4. Under **Build and deployment**, set **Source** to **Deploy from a branch**, branch **main**, folder **/ (root)**.
5. Save. After a minute the site is live at `https://<your-user>.github.io/onco-registry-mockup/`.

## Suggested demo path

Project Home > Online Designer > Data Dictionary > Survey Preview > Connect EHR > FHIR Field Mapping >
Visualization > Data Exports. The Project Home page lists this path with one click links.

## The example content

Survey instruments cover eligibility, demographics, cancer history, patient reported outcomes, complementary
and integrative therapies, and dietary supplements. The patient reported outcome items are original example
items written for this demonstration. They are not a licensed instrument such as FACT-G, EORTC QLQ-C30, or
PROMIS. Replace them with a licensed instrument under its own terms before any real use.

The two EHR instruments are read only and map from standard FHIR resources. Structured data (problems,
medications, labs, vitals, procedures) arrive first. Unstructured data (clinical notes, pathology, radiology)
are added in a second phase, with a natural language step that extracts concepts from free text.

## Note

No real data are collected. Health system names, participants, and values are fabricated for the demo.
