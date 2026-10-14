---
project_outline: "[[00-project/01-project-outline]]"
---

<!-- 5xl -->
# Low-Cost Headless CMS and Static Site Generator

A lightweight CMS that publishes websites as static files  
**Demonstration case:** a restaurant website

Author: Kaan Saffet Cicu  
Neptun: BY55CS  
Supervisor: Gergely LABORCI  
Project ID: 2026-LG-03

---

## Problem

- Small organisations need editable websites but have limited hosting budgets.
- Traditional CMS installations may require a continuously running web application.
- Maintenance, security updates and hosting resources can be burdensome.
- Non-technical editors still need to update pages, records and files easily.
- For example, a restaurant owner may need to change menu prices and photos without hiring a developer.

---

## Problem: why existing approaches are challenging

- A fully dynamic CMS runs code for each visitor request.
- Purely hand-coded static sites are inexpensive to host but inconvenient for editors.
- Combining editing with secure publishing is the central design challenge.

**Goal: easy editing without an always-on public rendering server.**

---

## Proposed idea

A headless CMS with four independent parts:

1. **Static admin interface:** editors authenticate and manage content.
2. **Low-cost backend:** stores accounts, pages, structured records and files.
3. **Build workflow:** reads only published content and an external blog feed.
4. **Static public site:** a SvelteKit template generates deployable files.

The backend can provide managed APIs, but admin and public sites require no persistent rendering server.

---

## Main goal: low-cost architecture comparison

| Approach | Main question |
| --- | --- |
| Managed integrated backend (e.g. Supabase) | How much development and operations work does a hosted data/auth/storage service save? |
| Alternative hosted services (e.g. Firebase) | How do pricing, limits, security rules and ownership compare? |
| Self-hosted backend | Does infrastructure control justify persistent resource and maintenance costs? |

Compare storage, authentication, files and hosting by pricing, free-tier limits, ownership, deployment effort, security and scale. Final choice is pending research.

---

## Target users

| Role | Needs | Interaction |
| --- | --- | --- |
| Site owner | Manage website/project and editor permissions | Site settings and access controls |
| Editor | Update content without writing code | Pages, structured records, files, publish |
| Visitor | View published content quickly | Public static website |

Primary audience: small organisations with simple sites and limited operational budgets. The first demonstration uses a restaurant, but the CMS is designed for other organisations too.

---

## Planned scope

**In scope**
- Secure registration/login and site-based authorisation
- Pages with slug, metadata, blocks/fields and publication state
- Generic structured data (shown as restaurant menu categories/items) and file upload/listing
- External blog integration (no CMS blog authoring)
- At least one SvelteKit static site template
- Protected GitHub-based build and deployment
- Validation and failure handling

**Out of scope for first version**
- Full drag-and-drop visual page builder
- Multiple template marketplace
- Multilingual content, search and advanced editorial workflows

---

## User journey 1: set up a website

1. Owner registers and signs in.
2. Owner creates a site and selects the initial template.
3. Owner sets the title, URL and optional external blog feed.
4. Backend validates and saves site configuration.

**Result:** a site ready for content editing.

---

## User journey 2: create content

1. Editor opens the Pages section and updates the restaurant About page.
2. Editor manages menu items as structured records, changing a dish price or description.
3. Editor uploads a food photo and inserts its reference.
4. Editor saves changes as draft, reviews, then marks them published.

**Result:** restaurant content is stored securely and ready for the next build.

---

## User journey 3: publish

1. Authorised editor requests publication.
2. Protected server-side mechanism starts the build workflow.
3. Build reads published CMS content and an external blog feed.
4. Template outputs HTML, CSS and JavaScript files.
5. GitHub-based static hosting deploys the successful build.

**Result:** visitors use a static website; no public application server is needed.

---

## User journey 4: errors and permissions

1. An editor attempts to upload an unsupported file: upload rejected.
2. A user attempts to modify another site's page: backend denies access.
3. Generation fails on invalid published data: build reports failure.
4. Previous successful static website remains available.

**Result:** secure boundaries and visible failure handling.

---

## Demo boundary

The restaurant is an example website, not the product itself. The product is a reusable headless CMS. Reservations, online ordering and payments are not included. External blog posts come from a provider feed.

---

## Next steps

- Research and compare backend/auth/file-storage/hosting alternatives, with current limits and costs.
- Finalise data model and access-control policies.
- Prototype static admin authentication and a single page CRUD workflow.
- Build the first SvelteKit template and generate a static page.
- Validate secure publication triggering through GitHub Actions.
