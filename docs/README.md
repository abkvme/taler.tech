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

- HTTP/2 on `:443` (IPv4 + IPv6)
- Strict-Transport-Security with `preload` + `includeSubDomains`
- `X-Content-Type-Options`, `X-Frame-Options: DENY`, `Referrer-Policy`,
  `Permissions-Policy`, `Cross-Origin-Opener-Policy`, `Cross-Origin-Resource-Policy`
- Content-Security-Policy that allows the existing CDN dependencies
  (`cdn.jsdelivr.net` for Bootstrap, `cdnjs.cloudflare.com` for Font Awesome)
- `Cache-Control` per asset class — 1 year immutable for CSS/JS/fonts,
  30 days for images, 1 hour for sitemaps, 5 minutes for HTML
- gzip for text resources
- Blocks dotfiles (`.git`, `.env`, …) and noisy WordPress scanner paths
- Serves `/.well-known/security.txt` and `/llms.txt` as `text/plain`
- Preserves the Certbot ACME challenge path

After the first deploy, once HSTS has been live for at least a day,
submit `taler.tech` at https://hstspreload.org/.
