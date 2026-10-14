---
project_outline: "[[00-project/01-project-outline]]"
---
# Low-Cost Headless CMS and Static Site Generator

## 1. Actors and permissions
| Role | Needs | Responsibility / access |
| --- | --- | --- |
| Site owner / administrator | Configure a site and control who edits it | Creates/manages sites; manages membership and publication settings within authorised sites |
| Editor | Maintain site content without coding | Creates and edits pages and structured records; uploads files; requests publication where authorised |
| Public visitor | Read the published site | Accesses generated public pages and publicly published files only; no admin access |
| Build / publication workflow | Generate and deploy a site | Reads published content with server-side credentials, fetches external blog posts and writes static build output |
| External blog provider | Provide blog entries | Exposes an RSS/API feed; posts are not created or stored as authored CMS posts |

## 2. Use cases / user stories
### UC1: Register and sign in
- **Actor:** Site owner or editor.
- **Precondition:** The administration site is available as static files.
- **Main flow:** 1. User registers or signs in using the selected authentication provider. 2. Provider verifies identity. 3. Admin UI loads only data authorised for that user.
- **Alternative / error flows:** Invalid credentials or failed verification are rejected; no private content is returned.
- **Postcondition:** An authenticated session exists; access remains subject to backend authorisation.

### UC2: Create and configure a site
- **Actor:** Site owner.
- **Precondition:** Authenticated user has permission to create a site.
- **Main flow:** 1. Owner creates a site/project (a restaurant demo for the prototype). 2. Supplies site name, base URL and template settings. 3. System validates and stores configuration.
- **Alternative / error flows:** Invalid settings (e.g. malformed URL) are rejected.
- **Postcondition:** The site is associated with its owner and ready for content.

### UC3: Create, edit and publish a page
- **Actor:** Editor.
- **Precondition:** Editor has write access to the site.
- **Main flow:** 1. Editor creates/selects a page. 2. Enters title, unique slug, metadata and editable content blocks/fields. 3. Saves as draft. 4. Changes status to published when ready.
- **Alternative / error flows:** Missing title, invalid slug, duplicate slug or unauthorised changes are rejected.
- **Postcondition:** Page revision is saved with its publication state.

### UC4: Manage structured content
- **Actor:** Editor.
- **Precondition:** Editor is authorised for the site.
- **Main flow:** 1. Editor opens a structured collection (e.g. menu items). 2. Creates or updates a record with defined fields such as name, price, description and category. 3. Saves it with publication status if applicable.
- **Alternative / error flows:** Invalid field type or missing required fields produce validation errors.
- **Postcondition:** Structured record can be retrieved through the content boundary.

### UC5: Upload and use a file
- **Actor:** Editor.
- **Precondition:** Editor has access to upload files to the site.
- **Main flow:** 1. Editor selects a file, such as a photo of a dish. 2. System checks file type and size. 3. Backend stores the file and its metadata. 4. Editor uses its reference in page or menu content.
- **Alternative / error flows:** Unauthorised, oversized, unsupported or failed uploads return errors and do not create invalid content references.
- **Postcondition:** File is listed and accessible under its intended access policy.

### UC6: Integrate external blog posts
- **Actor:** Build workflow (external provider supplies posts).
- **Precondition:** A site has a configured supported blog feed.
- **Main flow:** 1. Static build fetches public posts from the external feed. 2. Feed entries are validated/normalised. 3. Template renders a blog index and/or links to posts.
- **Alternative / error flows:** Unavailable or malformed feeds do not leak credentials; the build fails clearly or applies a documented fallback.
- **Postcondition:** Published site includes the selected external blog integration without offering blog authoring in the CMS.

### UC7: Generate and deploy the static website
- **Actor:** Authorised editor initiates; build workflow executes.
- **Precondition:** Site has valid settings and a protected publication trigger is available.
- **Main flow:** 1. Editor requests publication. 2. A trusted server-side trigger authorises the request and starts a GitHub workflow (or an equivalent secure publication mechanism). 3. Workflow reads published content only and fetches external blog content. 4. Template generates static files. 5. Static host deploys the output and reports status.
- **Alternative / error flows:** Unauthorised trigger, content validation failure or build/deployment failure causes a visible error without replacing the previous successful site.
- **Postcondition:** Latest successful published version is accessible on the public host.

### UC8: Enforce cross-site access control
- **Actor:** Unauthorised user (negative test).
- **Precondition:** At least two sites/users exist.
- **Main flow:** 1. A user attempts to read or modify another site's private data/file. 2. Backend verifies user/site role. 3. Request is denied.
- **Alternative / error flows:** A public visitor may access only deliberately public, published output.
- **Postcondition:** No unauthorised read/write occurs.

### UC9: Recover from invalid input or failed publication
- **Actor:** Editor or build workflow.
- **Precondition:** An invalid record, failed file operation or generator error occurs.
- **Main flow:** 1. System detects and reports the failure. 2. Failed changes/builds are logged. 3. User can correct input or retry.
- **Postcondition:** No broken deployment is marked successful; previous successful public output is preserved.

## 3. Functional requirements
| Requirement | Acceptance criteria | Priority |
| --- | --- | --- |
| Users can register and sign in securely. | Given a valid account, signing in creates a valid session; invalid credentials cannot access private data. | Must |
| Access is authorised by site and role. | Given a user without permission, reads and writes to another site's private records are rejected at the backend. | Must |
| Owners can manage sites/projects. | Given an authorised owner, valid site settings can be created and updated. | Must |
| Editors can manage pages with metadata and slugs. | A page with valid title, unique slug, metadata and fields/blocks can be saved; invalid entries are rejected. | Must |
| Pages support draft/published states. | Draft pages do not appear in generated public output. | Must |
| Editors can manage structured records. | A valid record (e.g. a menu item with name, price and category) can be created, updated, listed and used by the template. | Must |
| Editors can upload, list and reference files. | Allowed file uploads succeed and are listed; failed or invalid uploads show an error. | Must |
| CMS content and presentation remain separate. | Generator consumes an explicit content interface rather than admin UI internals. | Must |
| At least one SvelteKit or Vue template is provided. | A custom template renders site data into static output. | Must |
| Blog content comes from an external provider. | A configured provider feed is consumed at build time; CMS does not provide blog post authoring. | Must |
| Only published content is generated. | Draft pages/records and private files are excluded from public build output. | Must |
| A protected publishing mechanism deploys static output. | An authorised trigger starts the workflow; a successful run deploys to a static host. | Must |
| Both admin and public frontends are statically hostable. | Built assets can be served without a continuously running application-rendering server. | Must |
| Validation and failures are covered. | Invalid records, file failures and generator/build failures produce explicit tested outcomes. | Must |
| Alternative backend and hosting approaches are compared. | Documented comparison covers cost, quotas, ownership, deployment effort, security and expected scale. | Must |
| Multiple templates or sites/domains are supported. | A user can select a second template or deploy additional domains. | Could |
| Version history, scheduled publishing, multilingual content. | Optional feature-specific tests pass if developed. | Could |

Priority: **Must** = required; **Should** = desirable; **Could** = optional extension.

## 4. Business rules and constraints
| Rule or constraint | Rationale |
| --- | --- |
| Frontend static files never include service-role credentials or GitHub deployment tokens. | Client bundles are publicly inspectable. |
| Backend access control is enforced on every private data/file operation. | Hiding controls in the UI is not adequate security. |
| Site membership determines content editing permissions. | Protects organisations' data from other users. |
| Each page has a unique slug within its site. | Prevents URL collisions. |
| Only explicitly published records are included in static generation. | Draft content must not become public. |
| Uploaded files have validated type/size and explicit public/private access rules. | Prevents unsafe publication and unsupported uploads. |
| External posts are fetched from a provider and not edited as CMS posts. | Preserves the defined headless integration scope. |
| GitHub workflow secrets are accessible only to trusted build contexts. | Keeps privileged operations out of client code. |
| The static site must remain accessible if the CMS backend is temporarily unavailable. | Public delivery should not depend on a running app server. |
| Do not commit tokens, personal information or production data. | Meets security and project requirements. |

## 5. User interface and workflow
- **Admin interface (authenticated static frontend):** Sign in/register; site selection/settings; Pages (list, edit, status); Structured data; Files; Publish/status. All operations use authenticated backend requests.
- **Public website (generated static output):** For the restaurant demo: Home, About, Menu and Contact pages, structured menu listings, external blog listing/links and published photos. These are examples rendered through a reusable CMS content boundary.
- **Typical workflow:** Sign in (UC1) → create/configure site (UC2) → edit content (UC3–UC5) → configure blog source (UC6) → publish (UC7) → visitors browse static output. Access is verified at each step (UC8), with failure handling (UC9).

## 6. Non-functional requirements
The following are initial targets and will be refined during research and prototyping.
| Requirement | How it will be verified |
| --- | --- |
| Zero continuously running custom render servers for admin/public site. | Inspect deployments and verify both sites serve static build artifacts. |
| Minimal recurring hosting expense for a small demo. | Compare at least two realistic stacks; document monthly estimates and quota assumptions. |
| Low persistent CPU/RAM consumption. | Identify always-on backend services and distinguish managed infrastructure from static hosting. |
| No privileged credentials in public JavaScript or version control. | Inspect generated bundles, workflow configuration and Git history for secret leakage. |
| Cross-site isolation and least-privilege backend access. | Execute negative tests with multiple test users/sites. |
| Repeatable deployment. | Deploy from a clean checkout using documented commands and CI workflow. |
| Validation and actionable errors. | Automated/manual tests for invalid slugs, unsupported files and build failures. |
| Reasonable generation time for a small website. | Measure a representative build and record its duration; establish a target after prototyping. |
| Public site remains available when backend is offline. | Temporarily simulate unavailable backend after a successful deployment. |

## 7. Demonstration scenario and scope boundary
The prototype will use a fictional restaurant website to demonstrate the CMS. An editor can maintain regular pages, upload restaurant and dish photos, manage structured menu categories and items (name, description, price, category), and publish the static site. An external blog feed may be used for news or recipes; articles will remain authored by the external provider, not by this CMS. The menu is an example of generic structured content, not a restaurant-specific database requirement. Table reservations, ordering, checkout, and payment are out of scope.

## 8. Open questions and risks
| Question / risk | Impact | Owner | Resolution / decision |
| --- | --- | --- | --- |
| Which backend best meets cost, authentication and storage needs? | Recurring cost, free-tier fit and complexity | Student | Compare managed options and a self-hosted alternative, citing current quotas and trade-offs. |
| How can a static admin securely start a GitHub build? | Secrets could leak or unauthorised builds could run | Student | Prototype an authenticated server-side trigger, e.g. protected serverless function; no GitHub tokens in client. |
| Will static generation work with authenticated/private CMS data? | Missing pages or exposed drafts | Student | Use build-time credentials in GitHub secrets and published-only queries; verify output. |
| How should public and private uploaded files be separated? | Information disclosure | Student | Define storage policies and check generated references. |
| Which external blog feed/API is reliable? | Feed format differences or build failure | Student | Select one provider, normalise its feed and define a fallback. |
| How should page blocks and structured schemas be represented? | Excess implementation complexity | Student | Start with a small documented schema and reusable block types. |
| How will GitHub Pages paths and custom domains affect routes? | Broken deep links/assets | Student | Test base paths and static routing during first deployment. |
| How do hosting quotas and terms change? | Cost projection could become outdated | Student | Record research date and links; avoid treating current free tiers as permanent guarantees. |
