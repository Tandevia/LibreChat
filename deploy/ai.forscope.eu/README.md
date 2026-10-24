# ai.forscope.eu

Tandevia Workspace on the DigitalOcean droplet `tandevia-gateway` (fra1, project Tandevia).
Models (and, once switched on, tools) come from the Forscope gateway, `gateway.forscope.eu`.

| what | where |
|---|---|
| checkout | `/opt/tandevia-workspace` (branch `tandevia`) |
| compose | `/opt/tandevia-workspace/deploy/ai.forscope.eu` |
| image | `ghcr.io/tandevia/librechat:tandevia`, built by GitHub Actions on every push to `tandevia` |
| data | `data-node/` (MongoDB), `meili_data/` (search index), `uploads/`, `images/` |
| secrets | `.env` (mode 600, never committed); copies in the macOS Keychain, service `ai.forscope.eu` |
| HTTPS | Caddy in this stack (`caddy` service, `Caddyfile`), certificate in `caddy/` |
| DNS | Cloudflare, zone forscope.eu: `A ai → 104.248.43.141`, DNS only (grey cloud) |
| models | all through `gateway.forscope.eu` (`librechat.yaml`) as the application "LibreChat (ai.forscope.eu)", key `TANDEVIA_GATEWAY_KEY`; the signed-in person's e-mail goes in `x-onesam-user`, so their role, budget and security policy apply there |
| sign-in | Google Workspace only, domains listed in `librechat.yaml` → `registration.allowedDomains` |

## Deploy / update

Automatic: every push to `tandevia` runs `.github/workflows/tandevia-image.yml`, which
builds the image and then deploys it over SSH. The package on GHCR is private; the
droplet pulls it with the job's short-lived `GITHUB_TOKEN`.

On the droplet the deploy key (repo secret `DROPLET_SSH_KEY`) is bound in
`/root/.ssh/authorized_keys` to the forced command `/usr/local/bin/deploy-workspace`:
`git pull`, `docker login` with the token from stdin, `docker compose pull && up -d`,
`docker logout`. The key can run nothing else.

Manual (same thing, with a token that can read the package):

```bash
echo "$GHCR_TOKEN" | ssh root@104.248.43.141 deploy-workspace
```

Nothing is built on the droplet.

## First admin

The first person to sign in with Google becomes an ordinary user. LibreChat has no
script for roles; set it in MongoDB (role values: `USER`, `ADMIN`):

```bash
docker compose exec mongodb mongosh LibreChat --quiet --eval \
  'db.users.updateOne({ email: "you@forscope.eu" }, { $set: { role: "ADMIN" } })'
```

## Google OAuth client

Google Cloud Console → APIs & Services → Credentials → OAuth client ID, type Web application:

- Authorised JavaScript origin: `https://ai.forscope.eu`
- Authorised redirect URI: `https://ai.forscope.eu/oauth/google/callback`

Put the client ID and secret into `.env`.

## History

Until 2026-10-24 the models came from a Bifrost-based gateway on this droplet
(`aigateway.forscope.eu`, Tandevia/bifrost), whose stack also ran Caddy. It was
switched off on Jakub's word; LibreChat now uses the Forscope gateway and runs its own Caddy.
