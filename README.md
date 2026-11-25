# Eclipse Subsonic — container distribution

A Subsonic 1.16.1 / OpenSubsonic server for Eclipse Music accounts, distributed as
a container image. **No source code is included in this repository or in the
image**; the image contains an obfuscated, minified server bundle.

- Image: `ghcr.io/johnneerdael/subsonic-server-for-eclipse-music:latest`
- Platforms: `linux/amd64`, `linux/arm64`
- Admin UI: `/admin`
- Subsonic API: `/api` (clients append `/rest`)

## Run

```sh
cp .env.example .env
# set ADMIN_PASSWORD
docker compose up -d
```

Then open `http://<host>:4533/admin`:

1. Sign in with the admin password.
2. **Sign in with TV code** and approve at `eclipsemusic.app/tv`.
3. Wait for the library refresh (first run can take a few minutes).
4. Copy the Subsonic URL, username and API key into your client.

Client URL with the default compose file: `http://<host>:4533/api`.

## Behind Caddy

`deploy/Caddyfile.example` exposes `/api/*` and `/admin*` over TLS. Set
`PUBLIC_URL=https://music.example.com` and `BASE_PATH=/api` so the admin UI shows
the right client address.

## What the image contains

- Node 22, ffmpeg, tini
- `/app/dist/server.mjs` — the obfuscated, minified bundle
- no source tree, no build tools, no tests

The bundle uses string-array/RC4 and control-flow obfuscation, so plain `grep`
does not reveal the endpoint strings. It is still a speed bump, not a secret
store: the strings are decoded at runtime and can be recovered by dynamic
analysis. Packet captures remain possible. The Eclipse account
token lives in `/data` and must be protected.

## Credentials

`ADMIN_PASSWORD` is required. `SUBSONIC_PASSWORD`/`SUBSONIC_API_KEY` are generated
on first boot and printed in the container log if omitted; the admin UI shows them.
Data persists in `./data` (`library.db`, audio and artwork caches).
