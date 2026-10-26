# IVAR + I-Analyze — PWA Starter (Phase 1)

These are **installable launcher PWAs**, not yet full standalone versions of your Apps Script portals. They preserve your working Google Apps Script authentication, CSV import, Google Sheets operations, and IVAR grade-level colors by opening your *existing* deployed Apps Script portal. They do **not** cache student records, PINs, or teacher credentials. The launcher saves only the public web-app URL locally.

## Publish the two launchers on HTTPS

1. Create a GitHub repository (for example `school-apps`) and upload the contents of this package so that the `ivar/` and `i-analyze/` folders are at its root.
2. Under repository Settings > Pages, select **Deploy from a branch**, main, root. GitHub Pages will serve your static files over HTTPS. Public GitHub Pages repositories are publicly accessible, so do not put any student records, PINs, secrets, or private code in the repository.
3. Visit `https://YOUR-USERNAME.github.io/school-apps/ivar/` and `https://YOUR-USERNAME.github.io/school-apps/i-analyze/` (adjust for your repo name).
4. On each launcher, paste the corresponding deployed Google Apps Script **/exec** URL and click Save. The launcher's Open button takes you to your real working portal.
5. In Android Chrome, use Add to Home screen / Install app. In desktop Chrome, use the browser's install option when offered. Availability and install behavior vary by browser.

## Limitations of Phase 1

- The launchers themselves are installable PWAs with manifest, icons, service worker and offline cached shell. The *actual grade portal* still runs on Google Apps Script and needs internet access.
- Navigation to `script.google.com` leaves the launcher's PWA scope, so the portal may open in the browser, not in the same standalone app window.
- Service worker caches only launcher files, **never** student grades, passwords, PINs, or ZipGrade CSVs.
- The included icons are placeholder monograms; replace with approved brand artwork later.

## Phase 2 — Full standalone PWAs (required before reliable Android packaging)

1. Host the full IVAR and I-Analyze frontends at their own HTTPS paths/domains, keeping the original grade-dependent CSS and dashboard logic.
2. Replace `google.script.run` with authenticated HTTPS API calls. Apps Script HTML service methods cannot be called directly from an external domain. An Apps Script `doPost` endpoint is not a drop-in browser API: CORS/preflight, redirects, auth, and session handling must be addressed. Prefer a proper authenticated backend (for example Cloud Run / Firebase Functions) with Google Sheets access and server-side validation. Do not expose spreadsheet credentials or Apps Script secrets in client JS.
3. Keep student and teacher sessions separate, enforce authorization on *every* backend request, and restrict teacher CSV imports server-side. Never rely on client-side hiding for authorization.
4. Implement PWA manifests and service workers at the final frontend origins. Cache static assets only; avoid offline storage of student grades and identifiers without an approved privacy policy and threat model.
5. Test logins, IVAR grade 7–12 themes, passing/failing statuses, teacher CSV preview, duplicate prevention, and imported score destinations with non-production test data.

## Phase 3 — Android APKs

After the final PWAs are hosted and working in their own origin, package each as a Trusted Web Activity (TWA), for example using Bubblewrap. TWAs require HTTPS, a suitable PWA, and Digital Asset Links domain verification (`/.well-known/assetlinks.json`) to associate the Android app's signing certificate with its origin. For GitHub Pages project paths you generally cannot serve that verification file at the domain root; use a custom domain under your control or another host that allows root-level assetlinks.json. If TWA verification fails, behavior can fall back to a browser custom tab. Android app signing and Play Store publication are separate steps.

For school deployment, review student privacy, consent, Google Workspace policies, and who can access the underlying Apps Script web app before distributing installation links.
