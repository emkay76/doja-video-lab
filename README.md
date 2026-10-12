# Lumora — AI video studio

Create high-quality AI video three ways, all powered by [fal.ai](https://fal.ai):

| Mode | What it does | Models |
| --- | --- | --- |
| **Text → video** | Describe a scene | Veo 3.1 (+Fast), Kling 3.0 Pro, Seedance 2.0, Kling 2.5 Turbo Pro, LTX 2.3 Fast |
| **Image → video** | Animate a photo (optional end frame) | Veo 3.1 (+Fast), Kling 3.0 Pro, Seedance 2.0, Kling 2.5 Turbo Pro, LTX 2.3 Fast |
| **Re-edit video** | Restyle / transform existing footage | Kling O3 Edit Pro, Kling O1 Edit, Lucy Edit Pro, Luma Ray 2 Modify, Wan 2.2 Restyle |

Extras: AI prompt enhancer (LLM via fal), style chips, direct-to-CDN uploads with progress, live queue/render tracking,
cancel, download, "re-edit this result", and a per-browser library stored in PostgreSQL.

## Setup

1. Get an API key at <https://fal.ai/dashboard/keys>.
2. Provide it either way:
   - **Server-side (recommended for deployments):** set `FAL_KEY` in the environment (see `.env.example`).
   - **In the browser:** click **Connect fal.ai** in the header and paste the key (kept in `localStorage`, sent to
     this app's server per request; takes priority over `FAL_KEY`).
3. `npx drizzle-kit push` to create the `generations` table, then `npm run dev`.

## How it works

- `src/lib/models.ts` — catalog of verified fal endpoints and how generic controls map to each model's input payload.
- `src/app/api/generate` — validates, builds the payload and submits to `queue.fal.run`; the job is stored in Postgres.
- `src/app/api/generations/[id]` — polled by the UI; syncs status/result from fal, so renders survive page reloads.
- `src/app/api/upload/initiate` — gets a pre-signed fal CDN URL so the browser uploads large files directly.
- `src/app/api/enhance` — rewrites rough ideas into rich cinematic prompts using `fal-ai/any-llm`.
