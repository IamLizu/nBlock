# nblock

Lean shell utility to manage Nginx `deny` rules without manually editing server blocks.

It supports:
- Add an IP block
- Remove an IP block
- List blocked IPs
- Global include bootstrap in `http {}` (layout-agnostic)
- Optional bind to a specific `server {}` block
- Optional Nginx test + reload

## Why

Manual edits to Nginx config for one-off abusive IPs are error-prone and slow.
`nblock` keeps blocks in a dedicated file and gives you a small CLI for day-to-day operations.

## Features

- `add <ip>`: append `deny <ip>;` if not present
- `remove <ip>`: remove matching deny line
- `list`: print blocked IPs
- `auto-setup`: inject global include into `http {}` in `nginx.conf`
- `servers --server-file`: list detected `server {}` blocks in a file
- `bind-server`: attach include to exactly one selected `server {}` block
- `--reload`: run `nginx -t` and reload Nginx
- default auto-setup: ensure global include wiring during add/remove
- `--no-auto-setup`: skip wiring step if already configured
- IPv4 validation
- Duplicate protection

## Requirements

- Linux host with Nginx installed
- `bash`
- root/sudo permissions for `/etc/nginx/*`

## Install

### One-line install (recommended for first-time use)

```bash
curl -fsSL https://raw.githubusercontent.com/IamLizu/nBlock/main/nblock \
| sudo tee /usr/local/bin/nblock >/dev/null \
&& sudo chmod 0755 /usr/local/bin/nblock \
&& sudo nblock auto-setup
```

Then block an IP:

```bash
sudo nblock add 191.96.67.213 --reload
```

Alternative: bind only one server block (scope-limited mode):

```bash
sudo nblock bind-server --server-file /etc/nginx/nginx.conf --server-name example.com --reload
```

### Local install

```bash
sudo install -m 0755 nblock /usr/local/bin/nblock
```

Then use `nblock` instead of `./nblock`.

## Quick Start

1) Global block in one command:

```bash
sudo nblock add 191.96.67.213 --reload
```

2) Optional per-server mode (if you do not want global scope):

```bash
sudo nblock servers --server-file /etc/nginx/nginx.conf
sudo nblock bind-server --server-file /etc/nginx/nginx.conf --server-name example.com --reload
sudo nblock add 191.96.67.213 --reload
```

List blocked IPs:

```bash
sudo nblock list
```

Remove an IP:

```bash
sudo nblock remove 191.96.67.213 --reload
```

## Commands

```text
nblock add <ip> [--reload] [--no-auto-setup]
nblock remove <ip> [--reload] [--no-auto-setup]
nblock list
nblock auto-setup
nblock servers --server-file <path>
nblock bind-server --server-file <path> [--server-name <name>] [--listen <value>] [--index <n>] [--reload]
```

## Per-Server Binding (Optional)

Use `bind-server` when one file contains multiple `server {}` blocks.

It matches blocks by selector:
- `--server-name` (preferred)
- optional `--listen`
- optional `--index` (tie-breaker)

If multiple blocks match, it prints candidates and exits safely.

It inserts this line inside exactly one selected block:

```nginx
include /etc/nginx/blockips.conf;
```

## How Auto-Setup Works (Global Default)

`auto-setup` injects this include directly into `http {}` in `nginx.conf`:

```nginx
include /etc/nginx/blockips.conf;
```

This is layout-agnostic, so it works whether your server blocks live in:
- `nginx.conf`
- `conf.d/*.conf`
- `sites-enabled/*.conf`

## Environment Variables

You can override paths:

- `BLOCKLIST_FILE` (default: `/etc/nginx/blockips.conf`)
- `NGINX_CONF` (default: `/etc/nginx/nginx.conf`)

Example:

```bash
BLOCKLIST_FILE=/tmp/blockips.conf nblock list
```

## Production Notes

- Prefer upstream controls (WAF/security groups) for large-scale threat filtering.
- Use this tool for fast, host-level tactical blocks.
- Always keep `nginx -t` before reload (already done with `--reload`).
- Ensure logs contain real client IP if behind ALB/proxy.

## Example Workflow

```bash
# 1) block abusive IP globally
sudo nblock add 191.96.67.213 --reload

# 2) verify
sudo nblock list

# 3) unblock later if needed
sudo nblock remove 191.96.67.213 --reload
```

## License

MIT (or your preferred license).
