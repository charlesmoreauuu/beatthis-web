# chamor-legal

Static legal pages for [chamor.app](https://chamor.app).

## Pages

- `/` &mdash; minimal landing with links to the two policies
- `/privacy` &mdash; Privacy Policy
- `/terms` &mdash; Terms of Service

## Deploying to chamor.app via Cloudflare Pages

### One-time setup (~15 min)

1. **Create a GitHub repo.**
   ```sh
   cd ~/Desktop/chamor-legal
   git init
   git add .
   git commit -m "Initial chamor-legal"
   gh repo create chamor-legal --public --source=. --push
   ```
   (Or use the web UI if `gh` isn't installed.)

2. **Sign in to Cloudflare** at https://dash.cloudflare.com (free tier is enough).

3. **Add chamor.app to Cloudflare.**
   - Dashboard &rarr; "Add a site" &rarr; type `chamor.app` &rarr; Free plan.
   - Cloudflare gives you two nameservers (e.g. `dale.ns.cloudflare.com`, `ines.ns.cloudflare.com`).
   - At Namecheap: Domain List &rarr; Manage &rarr; Nameservers &rarr; Custom DNS &rarr; paste both.
   - Wait for propagation (anywhere from a few minutes to 24h; usually under 1h).

4. **Re-add DNS records you need.**
   When you move nameservers to Cloudflare, your existing DNS records (Resend DKIM/SPF/DMARC for `noreply@chamor.app`) must be recreated in Cloudflare.
   - In Cloudflare &rarr; chamor.app &rarr; DNS &rarr; Records, re-add:
     - the **MX**, **TXT (SPF)**, **CNAME (DKIM)**, and **TXT (DMARC)** records from your Resend setup
   - Verify in Resend dashboard that the domain still shows green.

5. **Create the Pages project.**
   - Cloudflare dashboard &rarr; "Workers & Pages" &rarr; "Create" &rarr; "Pages" &rarr; "Connect to Git".
   - Select your `chamor-legal` repo.
   - Build settings: leave everything blank (no build command, no build output directory &mdash; static files at the repo root).
   - Click "Save and Deploy". You'll get a `chamor-legal.pages.dev` URL.

6. **Attach custom domain.**
   - In the Pages project &rarr; "Custom domains" &rarr; "Set up a custom domain" &rarr; `chamor.app`.
   - Cloudflare auto-creates the CNAME for you.
   - Visit https://chamor.app and confirm the landing page loads. `/privacy` and `/terms` should work without `.html` (Cloudflare Pages handles that automatically).

### Updating later

```sh
cd ~/Desktop/chamor-legal
# edit privacy.html / terms.html / style.css
git add . && git commit -m "Update privacy: ..."
git push
```
Cloudflare auto-rebuilds on push (~30s).

## Notes on the documents

These are good-faith templates, not legal advice. They reflect what Chamor actually does (Supabase + Resend + Expo, no analytics, no ads) and cover the common bases (GDPR, CCPA, App Store guidelines 1.2 and 5.1.1). Before shipping to App Store production, have them reviewed by a lawyer who handles consumer-mobile apps in your jurisdiction.

When you make material changes to either document:
- Update the &ldquo;Effective&rdquo; date at the top
- Tell users in-app (a one-time toast or a settings indicator)
