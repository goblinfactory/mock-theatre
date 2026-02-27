# Cloudflare Build Settings

These are the current settings for the `mock-theatre` project on Cloudflare, as of 2026-02-27.

| Setting | Value |
| :--- | :--- |
| **Git repository** | `goblinfactory/mock-theatre` |
| **Production branch** | `main` |
| **Build command** | `npm run build` |
| **Deploy command** | `npx wrangler deploy` |
| **Version command** | `npx wrangler versions upload` |
| **Root directory** | `/` |
| **Build watch paths** | `*` |
| **Build cache** | Enabled |
| **Compatibility Date** | `2026-02-03` (from `wrangler.json`) |

## Observations

- The project uses `wrangler.json` (Workers with Assets) instead of a traditional Cloudflare Pages project.
- The `build` command in `package.json` currently includes `rm -f dist/_redirects`, which is likely preventing SPA routing from working.
- The user mentioned that previous attempts to add `_redirects` caused "weird ass errors" on Cloudflare. This might be due to the file format or how Cloudflare Workers for Assets handles redirection differently than standard Pages.
