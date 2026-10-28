# softnix.online

This is a static GitHub Pages website with a Cloudflare Worker for quotation email delivery. `index.html` contains the responsive single-page site, styles, and client-side interactions. The primary navigation points to Home, About, Products, Custom Development, Pricing, and Contact sections; Get Quote opens the quotation form.

## Local preview

Open `index.html` in a browser or run a local static server from the repository root.

## Quotation form setup

The quotation form posts to `https://api.softnix.online/send-quotation`. The Cloudflare Worker in `worker/` validates requests and sends the inquiry through Resend to `contact@softnix.online`. The Resend API key is a Worker secret and must never be added to client-side code or committed to the repository.

### Configure email delivery

1. If a Resend API key has been shared or exposed, revoke it in Resend and create a replacement. Do not paste the replacement into chat or source files.
2. Verify `softnix.online` in Resend and add the DNS records Resend provides to the Cloudflare zone. The sender is configured as `contact@softnix.online`; Resend must verify the domain before delivery.
3. From the repository root, deploy the Worker:

   ```powershell
   npx wrangler deploy --config worker/wrangler.toml
   ```

4. Add the replacement API key as a Cloudflare Worker secret. Wrangler prompts for the value without putting it in the command:

   ```powershell
   npx wrangler secret put RESEND_API_KEY --config worker/wrangler.toml
   ```

5. In Cloudflare, add a rate-limiting rule for `api.softnix.online/send-quotation` (for example, block an IP after 5 requests in 10 minutes) before publishing the form. The Worker also restricts website origins, validates fields, limits request size, and includes a honeypot.
6. Submit a test inquiry from `https://softnix.online/` and confirm it arrives at `contact@softnix.online`. The success message is shown only after Resend accepts the send request.

The Worker route in `worker/wrangler.toml` requires `softnix.online` to be an active Cloudflare zone. GitHub Pages continues to serve the website; Cloudflare handles the API subdomain. GitHub Pages does not process or store submissions.

No verified email, telephone number, WhatsApp number, product names, screenshots, product prices, license terms, or support terms were available in the original site. Add verified details before publishing those claims or contact links. The catalog intentionally avoids inventing products.

## Deployment

Publish the website using the existing GitHub Pages configuration and keep the `CNAME` file so the custom domain remains configured. Push the site changes to the branch and directory configured for GitHub Pages. Deploy Worker changes separately with Wrangler as described above; no application build or database is required.

After deployment, verify `https://softnix.online/`, the inquiry flow with a configured recipient, and the sitemap at `https://softnix.online/sitemap.xml`.
