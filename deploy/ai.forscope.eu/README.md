# ai.forscope.eu

Tandevia Workspace on the DigitalOcean droplet `tandevia-gateway` (fra1, project Tandevia),
next to Tandevia Gateway.

| what | where |
|---|---|
| checkout | `/opt/tandevia-workspace` (branch `tandevia`) |
| compose | `/opt/tandevia-workspace/deploy/ai.forscope.eu` |
| image | `ghcr.io/tandevia/librechat:tandevia`, built by GitHub Actions on every push to `tandevia` |
| data | `data-node/` (MongoDB), `meili_data/` (search index), `uploads/`, `images/` |
| secrets | `.env` (mode 600, never committed); copies in the macOS Keychain, service `ai.forscope.eu` |
| HTTPS | Caddy in the gateway stack, site `ai.forscope.eu` → `workspace:3080` over the `edge` network |
| DNS | Cloudflare, zone forscope.eu: `A ai → 104.248.43.141`, DNS only (grey cloud) |
| models | all through Tandevia Gateway (`librechat.yaml`), with the virtual key `TANDEVIA_GATEWAY_KEY` |
| sign-in | Google Workspace only, domains listed in `librechat.yaml` → `registration.allowedDomains` |

## Deploy / update

```bash
cd /opt/tandevia-workspace && git pull
cd deploy/ai.forscope.eu && docker compose pull && docker compose up -d
```

Nothing is built on the droplet. A code change goes: push to `tandevia` → Actions builds
the image → `docker compose pull && up -d` here.

## First admin

The first person to sign in with Google becomes an ordinary user. Promote them:

```bash
docker compose exec api npm run set-role -- --email you@forscope.eu --role ADMIN
```

(check `config/` in the repository for the current script names).

## Google OAuth client

Google Cloud Console → APIs & Services → Credentials → OAuth client ID, type Web application:

- Authorised JavaScript origin: `https://ai.forscope.eu`
- Authorised redirect URI: `https://ai.forscope.eu/oauth/google/callback`

Put the client ID and secret into `.env`.
