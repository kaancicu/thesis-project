---
name: "Kaan Saffet Cicu"
neptun: "BY55CS"
id: "2026-LG-03"
supervisor: "Gergely LABORCI"
github: "https://github.com/kaancicu/thesis-project"
---

# Low-Cost Headless CMS and Static Site Generator

Small organisations need websites that are easy to update but inexpensive to operate. Traditional content management systems often require a continuously running application server, increasing maintenance and hosting costs. This project will design and implement a lightweight headless CMS with a statically hosted administration interface. Editors will manage pages, structured content, and uploaded files using a secure backend. A separate static-site generator will read published content and a feed from an external blog provider, apply a SvelteKit template, and produce a public website that can be hosted without a running application server. A GitHub-based build and deployment workflow will be investigated and implemented. The project will compare backend, authentication, storage, and hosting options before choosing an architecture suitable for low ongoing cost. A restaurant website will serve as the demonstration case, with menu categories, dishes, prices, photographs and general information pages. The CMS itself will remain reusable for other types of organisations.

## Objectives
- **Primary objective:** Build a working, version-controlled headless CMS that manages content securely and publishes a generated static website.
- **Target users / stakeholders:** Small organisations with limited budgets; editors maintaining pages and files; public website visitors; site owners managing access.
- **Measurable success criteria:**
  - An authorised editor can create a site, add and update pages and structured records, upload files, and change publication status.
  - In the restaurant demo, an editor can update menu items and prices and publish the changes to the static website.
  - Unauthorised users cannot read private content or change another site's data.
  - The generator produces pages for published content only and integrates posts fetched from an external blog provider without treating them as CMS-authored records.
  - Both the administration interface and public website can be served as static files, while the selected backend supplies data, authentication, and file operations.
  - A content change can trigger or initiate a protected GitHub-based build and deployment.
  - Invalid content, failed uploads and failed generation are handled and tested.
  - At least two alternative approaches are compared for storage, authentication, file storage and hosting, with documented assumptions and current pricing/limits.
- **Constraints:** TypeScript throughout; SvelteKit or Vue for templates and generation; secure authentication/authorisation; no privileged secrets in browser bundles or Git; minimise persistent RAM/CPU and recurring cost; use Git for version control.

## Scope
### In scope
- A statically deployable editor administration interface with registration, login and secure access control.
- Site/project management and editable pages with slugs, metadata, content fields/blocks, and draft/published states.
- Generic structured records (demonstrated using restaurant menu categories and dishes) and file upload/listing/usage.
- A headless data/API boundary between management and presentation.
- At least one customisable SvelteKit website template.
- A static build incorporating published CMS content and external blog posts, such as a public RSS feed from Substack or Medium.
- A secure publication flow and deployment to a static host, preferably with GitHub Actions.
- Validation, negative-case testing, architecture documentation and a low-cost technology comparison.

### Out of scope (initial version)
- Developing an in-house blogging platform or storing externally authored blog posts as CMS content.
- A drag-and-drop page designer or rich collaborative editing.
- Multiple selectable templates, multilingual content and search (possible extensions).
- Running a permanent custom SSR web server.
- Enterprise-scale traffic, billing, and production-grade multi-region availability.

## Notes
The initial candidate stack is SvelteKit + TypeScript for the admin and website template, a managed backend such as Supabase for database/auth/files, GitHub Actions for generation, and static hosting such as GitHub Pages. This is a **provisional design**, not a completed research conclusion: alternatives, permissions and free-tier limits must be compared and verified before final selection. A protected server-side workflow will fetch published content for generation; privileged credentials must never appear in browser-delivered JavaScript. The first prototype will focus on one restaurant demonstration site and one template while retaining site identifiers and generic content structures in the data model. The system is not intended to be a restaurant booking, ordering or payment platform. The exact method for securely triggering publication from a static admin interface remains a design decision to validate.
