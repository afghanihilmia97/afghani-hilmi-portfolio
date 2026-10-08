# Afghani Hilmi portfolio

Bilingual portfolio with persistent admin CMS, contact inbox, project galleries, shareable case studies, certificates, real activity counts, availability, and notes. Public content uses the existing HTML/CSS design. Vinext/Workers handles routes; Sites provisions D1 and R2.

Admin access requires trusted ChatGPT identity headers and the hosted ADMIN_EMAIL owner and ADMIN_EMAILS comma-separated additional admin allowlist. Public form messages are delivered to the admin inbox, not an external email provider. Admin visits are excluded from analytics. Sessions are tab-local anonymous identifiers, not unique people. The marketing dashboard on the public home page remains an explicitly illustrative example.

The supplied CV and project screenshots are the source of the initial content. Preview videos loop the actual screenshots and are labeled accordingly. Upload real recordings through the admin project editor. Certificates without supplied images link to the relevant CV page and are labeled as listed in the CV.

## Development

Use the Sites execution-profile and dependency installation helpers. Build with the Sites build-site helper. Local tests: `node tests/runtime.mjs`. Tests use isolated SQLite and in-memory R2; they never write production records.

## Persistence

Schema is in db/schema.ts. Generate migration changes with Drizzle Kit, commit generated drizzle migrations, and publish through the Sites workflow. Runtime code never creates or alters the schema. Content uses an optimistic version to prevent competing tabs overwriting changes. CMS uploads are durable R2 objects addressed through /media/:id. Keep runtime values out of source and configure ADMIN_EMAIL and ADMIN_EMAILS through Sites.
