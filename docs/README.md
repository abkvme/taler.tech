# Operations docs

Reference configuration and deployment notes for `taler.tech`.

## `nginx.conf`

Hardened nginx site configuration. Drop in as
`/etc/nginx/sites-available/taler.tech` (replacing the current
Certbot-managed file) and reload nginx:

```
sudo cp docs/nginx.conf /etc/nginx/sites-available/taler.tech
sudo nginx -t
sudo systemctl reload nginx
```

What it adds compared to the previous config:

- **HTTP/2** on TCP/443 (IPv4 + IPv6) — the floor; every modern browser
  speaks it and HTTP/3 falls back to it on lossy networks or when UDP is
  blocked.
- **HTTP/3 (QUIC)** on UDP/443 (IPv4 + IPv6), advertised via the `Alt-Svc`
  response header so clients upgrade on subsequent connections. HTTP/3 is
  *additive* to HTTP/2, not a replacement — both are required.
- Strict-Transport-Security with `preload` + `includeSubDomains`
- `X-Content-Type-Options`, `X-Frame-Options: DENY`, `Referrer-Policy`,
  `Permissions-Policy`, `Cross-Origin-Opener-Policy`, `Cross-Origin-Resource-Policy`
- Content-Security-Policy that allows the existing CDN dependencies
  (`cdn.jsdelivr.net` for Bootstrap, `cdnjs.cloudflare.com` for Font Awesome)
- `Cache-Control` per asset class — 1 year immutable for CSS/JS/fonts,
  30 days for images, 1 hour for sitemaps, 5 minutes for HTML, no-cache on 404
- gzip for text resources
- Blocks dotfiles (`.git`, `.env`, …) and noisy WordPress scanner paths
- Serves `/.well-known/security.txt`, `/security.txt` (legacy alias),
  `/llms.txt`, `/humans.txt` as `text/plain`
- Language-aware custom 404 (Hugo-rendered, picked by URL prefix)
- Preserves the Certbot ACME challenge path

## HTTP/3 prerequisites

The `quic`, `http3`, and `ssl_early_data` directives require:

1. **nginx 1.25.0 or newer** built with `--with-http_v3_module`. The
   official nginx repository (`nginx.org/packages/`) ships a build that
   includes it. Distro packages on Debian 12 / Ubuntu 22.04–24.04 do **not**:
   you'll see `unknown directive "http3"` from `nginx -t`.
2. **UDP/443 open** in the host firewall and in any cloud provider security
   group:

   ```
   ufw allow 443/udp           # if using ufw
   iptables -A INPUT -p udp --dport 443 -j ACCEPT   # if using raw iptables
   ```

If you can't upgrade nginx right now, comment out these lines and
HTTP/2 will keep serving the site fine:

```
# listen 443 quic reuseport;
# listen [::]:443 quic reuseport;
# http3            on;
# quic_retry       on;
# ssl_early_data   on;
# add_header Alt-Svc 'h3=":443"; ma=86400' always;   (also in location / and the 404 location)
```

## Verifying

After reloading nginx:

```sh
# HTTP/2 over TCP
curl -sI --http2 https://taler.tech/ | head -1   # → HTTP/2 200
# HTTP/3 over QUIC (curl 7.66+ with --with-quic)
curl -sI --http3 https://taler.tech/ | head -1   # → HTTP/3 200
# Alt-Svc advertisement on HTTP/2 response
curl -sI https://taler.tech/ | grep -i alt-svc   # → alt-svc: h3=":443"; ma=86400
```

## After the first deploy

Once HSTS has been live for at least a day, submit `taler.tech` at
https://hstspreload.org/.
