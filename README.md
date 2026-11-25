# Blink

A small local one-page site builder. Choose a template, edit directly on the canvas, preview desktop and mobile layouts, and publish a snapshot to a local URL.

## Run

Requires Node.js 20 or later. No package installation is needed.

```sh
npm run dev
```

Open http://localhost:3000/dashboard. Describe a site to Blink AI in plain English, or start from a profile, product launch, portfolio, or blank template. The dashboard lets you edit, duplicate, and delete sites. Published pages are available at `http://localhost:3000/p/<slug>`.

## AI page generation

The server reads `GROQ_API_KEY` from a local `.env` file (ignored by Git) or from its environment. Put this line in `Blink/.env` if you are setting up a new checkout:

```sh
GROQ_API_KEY=your_api_key_here
```

Run `npm run dev`. The dashboard AI prompt accepts a brief such as “portfolio for a wedding photographer in Jaipur” and creates a styled, editable draft with sample copy. In the editor, use the Blink AI panel to request changes such as “Make the headline more playful”; the change can be undone. It uses Groq Chat Completions with strict Structured Outputs. The default model is `openai/gpt-oss-120b`; set `GROQ_MODEL` to another Groq model with strict JSON Schema support if you prefer. Prompts and current page content are sent to Groq and API usage may incur charges. The key stays on the server. Generated image blocks are placeholders for your own photos; you can upload images in the editor.

Blink does not meter AI requests with an in-app credit balance. Groq provider rate limits and API availability can still affect requests.

Draft and published data are stored in `data/sites.json`. Uploaded images are stored in `data/assets/`, and form submissions in `data/submissions.json`. The app imports the earlier single-site files automatically. PNG, JPG, WebP, GIF, and AVIF images up to 20 MB can be uploaded from your computer or linked by URL; embeds use external URLs. By default, Blink binds to `127.0.0.1`; links to localhost work only on this computer.

## Publish on the public web

The Publish button immediately saves a public snapshot at `/p/<slug>`. To make that link available to anyone, deploy Blink to a host with HTTPS and a persistent disk. The included Dockerfile runs the same app in hosted mode. Set these environment variables on the host:

```text
HOST=0.0.0.0
PORT=3000
DATA_DIR=/app/data
ADMIN_PASSWORD=<a-long-unique-password>
PUBLIC_BASE_URL=https://your-domain.example
GROQ_API_KEY=<your-existing-key>
```

Mount a persistent disk at `/app/data` so published pages and uploaded images survive restarts. `ADMIN_PASSWORD` protects the dashboard, editor, and write APIs with HTTP Basic authentication; the landing page, pricing page, and published sites remain public. Use HTTPS because browsers otherwise send Basic credentials without transport encryption. If `PUBLIC_BASE_URL` is set, Publish returns the canonical HTTPS link; otherwise it uses the current browser origin. The same button republishes edits to the existing link. The local workspace shows an honest local-only message until Blink is deployed.

This is a single-admin deployment. Before offering public user accounts or paid hosting, add account authentication, per-user storage, abuse controls for forms and AI requests, and managed backups.

## Pricing preview and trial

`/pricing` shows India (INR) and global (USD) prices with monthly and yearly options. Free is ₹0 / $0. Pro is ₹79/month or ₹599/year in India, and $1.69/month or $12/year globally. Business is ₹199/month or ₹1,499/year in India, and $3.99/month or $29/year globally. A proposed lifetime launch offer is ₹1,999 / $39 for the first 100 paying customers. Paid checkout and custom domains are not available yet, so no purchase or lifetime spot is claimed. The 7-day Pro trial works in the local workspace without a card, hides the published-page badge until the trial expires. Trial state is stored in `data/plan.json`. This local-only trial is not an account or billing system.

## Visual editor controls

The editor supports page background images, uploaded images, shapes, direct text editing, colors, Google Fonts, transparency, and separate desktop and mobile positions. Changes are saved in the site's JSON and rendered on the published page.

## Freeform canvas

New pages start in the freeform canvas. Existing pages stay in their original vertical layout until **Enable canvas** is selected in the editor. Select an element and drag its top handle to move it, or drag an image or shape by its body. Drag the lower corner to resize it. Switch between Desktop and Mobile to set separate positions. The Layers list can be dragged to change stacking order; its arrow buttons work with a keyboard. Arrow keys nudge a selected element (Shift for 10-step moves), Delete removes it, and Cmd/Ctrl+D duplicates it. Undo, autosave, and publishing preserve the canvas. Layout is stored as a responsive horizontal percentage plus vertical pixel positions; the mobile layout is used on published pages at 700 px and below. This is one freeform page section, not Canva's full multi-page editor or real-time collaboration.

## Editor workspace updates

Elements, Layers, Design, and Blink AI now use focused tabs. The Design panel exposes text, links, image upload, typography, opacity, corners, and stacking controls. Images and shapes can be dragged by their bodies; selected elements retain dedicated drag and resize handles. Snap to a 5% horizontal and 20 px vertical grid is optional, and holding Alt while dragging bypasses it. Layers can be hidden or locked while editing. Hidden layers do not appear on published pages. Saves are queued in order so an older autosave cannot overwrite a newer one, and publishing waits for queued saves.
