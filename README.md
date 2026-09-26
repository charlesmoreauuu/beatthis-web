# beatthis-web

Static site for [beatthis.me](https://beatthis.me): the landing page, the legal
pages, and the invite-link handler for the BeatThis iOS app.

## Pages

- `/` &rarr; landing page with links to the two policies
- `/privacy` &rarr; Privacy Policy
- `/terms` &rarr; Terms of Service
- `/join/<CODE>` &rarr; group invite: opens the app, or offers the App Store
- `/add/<CODE>` &rarr; friend invite: same flow, friend-code wording

GitHub Pages serves `privacy.html` and `terms.html` at their extensionless
paths too, so `/privacy` and `/terms` both work. The app links to the
`.html` form (`components/Settings.js`, `components/Auth.js` in the app repo).

`404.html` is not just an error page. GitHub Pages has no server-side routing,
so every unmatched path falls through to it, and its inline script parses
`/join/<CODE>` and `/add/<CODE>` out of `window.location.pathname` and drives
the open-or-install flow. Editing `404.html` means editing the invite landing
page. The App Store id is hardcoded there (`id6780211717`).

## Hosting

GitHub Pages, deployed from the `main` branch at the repo root. There is no
build step and no framework. Push to `main` and Pages republishes in about a
minute.

Two files matter to the deploy and are easy to delete by accident:

- `CNAME` pins the custom domain to `beatthis.me`. DNS is four apex `A`
  records pointing at GitHub Pages, set at the registrar.
- `.nojekyll` disables Jekyll. Without it Jekyll strips dot-prefixed
  directories from the output and `.well-known/` never publishes, which
  silently breaks Universal Links.

`.well-known/apple-app-site-association` is the Universal Links manifest. It
carries the real Team ID and app ID (`YJBQ44DA6G.app.beatthis`) and must stay
in sync with `associatedDomains` in the app's `app.json`. It is served as
plain JSON with no extension, which is what Apple expects.

## Updating

```sh
git clone https://github.com/charlesmoreauuu/beatthis-web.git
cd beatthis-web
# edit index.html / privacy.html / terms.html / 404.html / style.css
git add . && git commit -m "Update privacy: ..."
git push
```

Then confirm the live page, since there is no staging environment:

```sh
curl -sI https://beatthis.me/privacy.html | head -1
```

## Notes on the documents

These are good-faith templates, not legal advice. They reflect what BeatThis
actually does (Supabase + Resend + Expo, no analytics, no ads) and cover the
common bases (GDPR, CCPA, App Store guidelines 1.2 and 5.1.1). Before shipping
to App Store production, have them reviewed by a lawyer who handles
consumer-mobile apps in your jurisdiction.

When you make material changes to either document:

- Update the "Effective" date at the top
- Tell users in-app (a one-time toast or a settings indicator)

Contact address on all three pages is `support@beatthis.me`, forwarded to a
personal inbox via ImprovMX.
