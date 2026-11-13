# Case Connect AI — deployment

## Vercel (frontend)

Import this repository into Vercel and keep the **Root Directory** set to `.` (repository root). The root `vercel.json` installs and builds only `frontend/`, publishing `frontend/dist`. This avoids packaging the heavyweight Python dependencies into a Vercel Function.

No Python service, Node gateway, database, or API function is deployed by this Vercel configuration. The frontend build can succeed independently, but live authentication, case management, evidence processing, graph analysis, and reports require the separately hosted backend and databases.

After deploying the FastAPI backend, add this Vercel environment variable and redeploy:

```env
VITE_API_URL=https://YOUR-BACKEND-HOST
```

Use the backend base URL without a trailing `/api`; the frontend API client appends endpoint paths. Configure the backend's CORS allowlist to include the exact Vercel production domain.

## FastAPI backend (separate host)

The backend is retained in `backend/`. It is not suitable for Vercel Functions in its current dependency configuration (the previous deployment bundled approximately 958 MB). Deploy it as a persistent Python/Docker web service on a host that supports larger application images and configure its environment variables, durable database, Neo4j connection, and persistent evidence storage there. The repository includes `backend/Dockerfile`; its container context must be the repository root.

Do not use the old mock `/api` handlers: they returned fabricated login tokens and placeholder data rather than authenticating users or serving real cases. They have been removed.
