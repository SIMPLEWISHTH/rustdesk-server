# rustdesk-server

RustDesk Server OSS deployment (hbbs + hbbr) for the SIMPLEWISHTH self-hosted
estate. Deployed through Coolify (docker compose build pack). No secrets in
this repository; the Ed25519 key pair lives only in the Docker volume on each
server.

## Layout

- Primary: `hetzner-sw02-fsn1` (49.13.122.55), Coolify project `RustDesk`,
  application `rustdesk-htzn01`.
- Standby (planned): Asia VPS with IPv4, same key pair, same compose (with the
  relay address in `hbbs -r` adjusted to that server's IP).

## Ports (public on the host)

| Port | Protocol | Service | Purpose |
|------|----------|---------|---------|
| 21115 | TCP | hbbs | NAT type test / service |
| 21116 | TCP + UDP | hbbs | ID registration, rendezvous, hole punching |
| 21117 | TCP | hbbr | relay (only used when direct P2P fails) |

21118/2119 (web client) are intentionally not exposed.

## Key handling

`hbbs`/`hbbr` run with `-k _`, so clients must have the public key configured.
The key pair is generated on first start inside the shared `rustdesk-data`
volume (`/root/id_ed25519`, `/root/id_ed25519.pub`). To stand up the standby
server with the same identity, copy both files into the standby volume before
its first start.

## Client configuration

- ID server: `rustdesk.simplewish.me` (DNS-only A record)
- Relay server: leave empty (hbbs hands out `49.13.122.55:21117` via `-r`)
- Key: the public key from the volume (stored in the estate secrets store)
