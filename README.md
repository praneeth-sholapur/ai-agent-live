# Praneeth's real AI assistant - deployment

## What this is
- `index.html`: the public site. Free-text chat calls POST /api/chat first, and falls back to the built-in answers if the backend is unavailable.
- `api/chat.js`: Vercel serverless function (Node). Calls Muse Wonder at https://api.meta.ai/v1/chat/completions, model muse-wonder-1.0, max_tokens 4000.
- `SYSTEM_PROMPT.md`: the system prompt. Private rules: never reveal salary, work authorization, visa status, or a phone number, and never mention a move away from contract work to full-time work.

## Deploy on Vercel (recommended)
1. Create a new GitHub repo (or a new branch) with these files at the root: index.html, api/chat.js, SYSTEM_PROMPT.md, vercel.json.
2. In Vercel, Import Project from that repo. Framework: Other. No build command needed.
3. In Vercel Project Settings -> Environment Variables, add:
   - META_API_KEY = your Meta Model API key from dev.meta.ai
   - MONTHLY_SPEND_CAP_USD = 50
   Never put the key in the repo, in index.html, or in chat.py.
4. Deploy. Open the site and test: "What is the NYC taxi pipeline?", "What salary does Praneeth expect?" (must deflect), "What is his work authorization?" (must deflect).
5. Keep the GitHub Pages site live until the Vercel site passes those tests, then update LinkedIn Featured and Contact Info to the Vercel URL.

## Cost guard
- Backend estimates $0.02 per reply as a safe overestimate. At that rate, $50 allows about 2,500 replies a month.
- Rate limit: 20 messages per minute per instance. For production traffic, move the rate limit and spend tracking to a shared store (Redis / Vercel KV).
- If the cap is hit, the API returns a paused message instead of calling the model.

## If the backend is down
- The frontend falls back to its built-in answers, so visitors still get a working chat.
