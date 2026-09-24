# Unloader — tester edition

What's inside:

- `index.html` — the whole app. Each tester's data is stored privately in their own browser.
- `netlify/functions/ai.mjs` — a small server function that sorts brain dumps and reads photos with Claude. Your API key lives only here.
- `netlify.toml` — hosting settings.

Without the server function, the app still works: tasks are sorted by keywords, and the Photo button is hidden.

## 1. Get an Anthropic API key

1. Sign in at https://console.anthropic.com and add billing.
2. Create an API key under **API Keys**.
3. Set a **monthly spend limit** in the Console's limits settings (for example $20). This caps your cost if the link spreads.

## 2. Deploy on Netlify

Netlify's drag-and-drop upload does **not** run server functions, so use one of these:

**Option A — GitHub (easiest to update later)**
1. Create a new GitHub repository and upload these files, keeping the folder structure.
2. In Netlify: **Add new site → Import an existing project → GitHub**, pick the repository. Leave build settings empty; `netlify.toml` handles them.

**Option B — command line**
```
npx netlify-cli login
npx netlify-cli deploy --prod
```
Run this inside this folder.

## 3. Add environment variables

In Netlify: **Site configuration → Environment variables**:

| Name | Value |
|---|---|
| `ANTHROPIC_API_KEY` | your key (required) |
| `ALLOWED_ORIGINS` | `https://unloader.app,https://www.unloader.app` (blocks other sites from using your function) |
| `RATE_LIMIT_PER_10_MIN` | optional, default `30` requests per person per 10 minutes |
| `MODEL_TEXT` / `MODEL_PHOTO` | optional, to change models |

Redeploy after adding them (**Deploys → Trigger deploy**).

## 4. Connect unloader.app

In Netlify: **Domain management → Add a domain → unloader.app**, then follow the DNS instructions at your domain registrar. HTTPS is set up automatically.

## 5. Check it works

Open `https://unloader.app/api/ai`. You should see `{"ok":true,...}`. If `ok` is `false`, the API key variable is missing.

## Optional: feedback link

At the top of the `<script>` in `index.html`, set `FEEDBACK_EMAIL` to your address to show a "Send feedback" link in the app.

## Good to know

- Photos are resized to about 1.5 MP on the tester's phone before upload, to keep cost and upload time low.
- The rate limit is best-effort. The spend limit in the Anthropic Console is your real safety net.
- The page is marked `noindex`, so it won't appear in search results while you test.
