# Auction System

This repository contains a simple auction application with a React (Vite) frontend and an Express + MongoDB backend.

This README covers how to run locally and how to prepare the project for production deployments on Vercel (frontend) and Render (backend).

---

## Project layout

- `client/` — Vite + React frontend
- `server/` — Express backend with Mongoose models

---

## Environment variables

### Server (Render)
Set these environment variables in the Render service settings for your web service.

- `MONGO_URI` — MongoDB connection string. Example:
  `mongodb+srv://username:password@cluster0.xxxx.mongodb.net/auction_db?retryWrites=true&w=majority`
  - If your password contains special characters (e.g. `@`, `:`), URL-encode them (e.g. `@` -> `%40`).
- `JWT_SECRET` — Secret used to sign JWT tokens. Choose a strong random string.
- `CLIENT_URL` — The deployed frontend URL. Example:
  `https://actsystem-egcke2nnv-navadeeps-projects-a0b61e16.vercel.app`

Notes for Render:
- Render sets `PORT` for you; the server uses `process.env.PORT` already.
- Add the `MONGO_URI` and `JWT_SECRET` in the Render service Environment tab.

### Client (Vercel)
Configure the following environment variable in your Vercel project settings (Production environment):

- `VITE_API_BASE_URL` — e.g. `https://auction-server-fq8h.onrender.com/api`

Also create a local `.env` (already provided) for development:
- `client/.env` — sets `VITE_API_BASE_URL` for local dev builds
- `client/.env.production` — used when building for production locally (optional)

Important: Vite embeds `VITE_*` variables at build time, so set the Vercel env variable before deploying/building the site.

---

## Deploying the server to Render

1. Create a new Web Service on Render and connect your GitHub repo.
2. Build command: none required (server uses Node). You can set it to `npm install`.
3. Start command: `npm start` (or `node index.js`).
4. Add environment variables: `MONGO_URI`, `JWT_SECRET`, `CLIENT_URL`.
5. Deploy and check the Render logs for successful MongoDB connection and that the server starts.

If you see DB auth errors, double-check the `MONGO_URI` (percent-encode special chars) and that the DB user has network access.

---

## Deploying the client to Vercel

1. In Vercel, create a new project and connect this repository.
2. Set the root for deployment to `client/`.
3. Build command: `npm run build` (Vite default). Output directory: `dist`.
4. Set environment variable `VITE_API_BASE_URL` to your server API base (e.g. `https://auction-server-fq8h.onrender.com/api`) in the Vercel project settings (Production).
5. Deploy.

Important: If `VITE_API_BASE_URL` is missing at build time, the production bundle will keep the fallback value and your client may attempt to call `localhost`.

---

## Quick local run

Server:

```powershell
# from repo root
cd server
npm install
# put a working .env (copy .env.example if available) then:
node index.js
```

Client:

```powershell
cd client
npm install
# for local dev (Vite dev server)
npm run dev
# to build production locally
npm run build
```

---

## CORS and troubleshooting

- The server includes an allowlist that uses `CLIENT_URL` and localhost origins. Ensure `CLIENT_URL` matches the deployed frontend origin exactly (including protocol and hostname).
- Check the browser DevTools Console/Network for CORS errors or blocked requests.
- To test the API from your machine (bypasses CORS):

```powershell
curl.exe -H "Content-Type: application/json" -X POST https://auction-server-fq8h.onrender.com/api/auth/register -d "{\"name\":\"Test\",\"email\":\"t@test.com\",\"password\":\"123456\"}"
```

If that returns a 201/200, the server is functioning and CORS or frontend config is likely the issue.

---

## Notes & recommendations

- Keep `JWT_SECRET` identical across deployments if you need existing tokens to remain valid.
- Use a cloud storage (Cloudinary / S3) for images rather than storing large base64 strings in the DB.
- Lock down CORS to only the exact frontend origin after testing.
- Do not commit secrets to source control. Use Render/Vercel environment variables.

If you want, I can:
- Patch `server/index.js` (done) to include more flexible CORS logic (already applied).
- Walk you through setting the environment variables on Render and Vercel step-by-step.
- Verify server logs on Render (you need to share log outputs).

---

Thank you — let me know which step to do next (set envs on Render, set Vercel env, or inspect logs/network requests).
