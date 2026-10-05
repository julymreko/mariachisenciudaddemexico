# AUD-003 Evidence - Production Architecture

Captured: 2026-10-04 local / 2026-10-05 UTC
Method: read-only public HTTP/TLS inspection and read-only SSH inspection on the production VPS.
Executor: Julian Cely with ChatGPT orchestration.

No production configuration was changed.

## Public HTTP behavior

### HTTPS apex

Command:

`curl.exe -I https://mariachisenciudaddemexico.com`

Observed:

- HTTP/1.1 301 Moved Permanently
- Server: Apache
- X-Powered-By: PHP/8.2.23
- X-Redirect-By: WordPress
- Location: https://www.mariachisenciudaddemexico.com/
- X-Powered-By: PleskLin
- X-Endurance-Cache-Level: 0
- X-nginx-cache: WordPress
- Content-Security-Policy: upgrade-insecure-requests;

### HTTPS www

Command:

`curl.exe -I https://www.mariachisenciudaddemexico.com/`

Observed:

- HTTP/1.1 200 OK
- Server: Apache
- X-Powered-By: PHP/8.2.23
- X-Powered-By: PleskLin
- X-Endurance-Cache-Level: 0
- X-nginx-cache: WordPress
- Content-Security-Policy: upgrade-insecure-requests;

A normal GET returned the same effective response headers.

### HTTP apex and www

Commands:

`curl.exe -I http://mariachisenciudaddemexico.com/`
`curl.exe -I http://www.mariachisenciudaddemexico.com/`

Observed for both:

- HTTP/1.1 200 OK
- Server: Apache
- Content-Length: 432
- X-Powered-By: PleskLin
- Content-Type: text/html

The returned body was the Plesk default page:

`<title>Web Server's Default Page</title>`

with the message:

`You see this page because there is no Web site at this address.`

## TLS

Certificate observed on `www.mariachisenciudaddemexico.com:443`:

- Subject: CN=mariachisenciudaddemexico.com
- Issuer: Let's Encrypt YR1
- NotBefore: 2026-07-08
- NotAfter: 2026-11-05
- SAN: mariachisenciudaddemexico.com
- SAN: www.mariachisenciudaddemexico.com

## Operating system and web stack

Commands included:

`cat /etc/os-release && uname -a`
`apache2 -v`
`nginx -v`
`plesk version`
`mysql --version`

Observed:

- Ubuntu 22.04.5 LTS (Jammy)
- Linux kernel 5.15.0-121-generic
- x86_64
- Apache 2.4.52
- nginx 1.18.0 installed
- Plesk Obsidian 18.0.63.4
- MariaDB 10.6.22

## Production domain hosting

Plesk domain information showed:

- Hosting type: Physical hosting
- IP: 74.208.94.17
- Document root: /var/www/vhosts/mariachisenciudaddemexico.com/httpdocs
- SSL/TLS support: On
- Certificate: Let's Encrypt mariachisenciudaddemexico.com
- Plesk reports "Permanent SEO-safe 301 redirect from HTTP to HTTPS: On"
- Database count: 1
- Disk used by httpdocs: approximately 694 MB

Sensitive account identifiers were intentionally excluded.

## Apache virtual hosts

`apache2ctl -S` showed active name-based vhosts for the production domain on both:

- 74.208.94.17:80
- 74.208.94.17:443

Aliases include:

- www.mariachisenciudaddemexico.com
- ipv4.mariachisenciudaddemexico.com

Apache is the active listener on public ports 80 and 443.

## HTTP vhost discrepancy

The effective Apache port 80 vhost contains the domain ServerName/ServerAlias but no site DocumentRoot.

The intended HTTPS redirect rules are present but commented:

`#RewriteEngine On`
`#RewriteCond %{HTTPS} off`
`#RewriteRule ^ https://%{HTTP_HOST}%{REQUEST_URI} [R=301,L,QSA]`

The main Apache DocumentRoot is:

`/var/www/vhosts/default/htdocs`

This matches the observed Plesk default page returned over HTTP.

The generated Plesk domain `httpd.conf` contains the same commented redirect rules, so the behavior is not caused by divergence between the generated and loaded Apache configuration.

## HTTPS vhost

The effective 443 vhost has:

- DocumentRoot: /var/www/vhosts/mariachisenciudaddemexico.com/httpdocs
- Let's Encrypt certificate paths under /etc/letsencrypt/live/mariachisenciudaddemexico.com/
- SSLRequireSSL
- PHP files handled through:
  `proxy:unix:/var/www/vhosts/system/mariachisenciudaddemexico.com/php-fpm.sock|fcgi://127.0.0.1:9000`

No persistent custom `vhost.conf` or `vhost_ssl.conf` files exist for this domain.

## PHP runtime

Plesk PHP handlers include PHP 8.2.23.

The web response exposes PHP 8.2.23.

Apache modules loaded for PHP/FastCGI:

- fcgid_module
- proxy_fcgi_module

The service:

`plesk-php82-fpm.service`

was active and running.

The server-global CLI PHP is 8.1.2, but that is not the production web runtime.

## WordPress

WP-CLI executed explicitly with Plesk PHP 8.2 returned:

- WordPress version: 6.9.9
- home: https://www.mariachisenciudaddemexico.com
- siteurl: https://www.mariachisenciudaddemexico.com
- DB_HOST: localhost

Therefore the production WordPress database is local to the VPS.

## .htaccess behavior

The active site `.htaccess` contains:

- WP Rocket 3.16.4 cache/expiry/compression rules
- standard WordPress rewrite rules to index.php
- Really Simple auto-prepend reference to wp-content/advanced-headers.php
- headers:
  - X-Endurance-Cache-Level: 0
  - X-nginx-cache: WordPress

The `X-nginx-cache` header is written by `.htaccess`; it is not proof of active nginx proxying.

The file also contains historical cPanel / EA-PHP / LSAPI directives referencing older PHP handlers. Apache currently has neither mod_php nor lsapi loaded, so those blocks are inactive under the observed runtime.

## nginx state

`systemctl is-active nginx` returned failed.
`systemctl is-active apache2` returned active.

nginx has been failed since 2026-09-15 because:

`nginx: [emerg] unknown directive "brotli" in /etc/nginx/conf.d/brotli.conf:1`

The file contains:

`brotli on;`

and a `brotli_types` directive.

The generated Plesk nginx configuration contains proxy targets to Apache/internal services, but nginx is not active and Apache is not currently listening on the expected internal 7080/7081 ports.

Therefore nginx is not part of the effective production request path at capture time.

## Direct-origin observation

DNS from AUD-002 points the apex directly to 74.208.94.17.

The Apache access log showed requests arriving from multiple public client/bot IP addresses directly, including the audit curl requests. No uniform proxy/CDN source layer was observed.

Current evidence therefore supports direct public traffic to the VPS rather than Cloudflare or another active reverse proxy/CDN in front of production.

## Disk state

`df -h` for the site filesystem showed:

- Size: 77G
- Used: 68G
- Available: 5.3G
- Use: 93%

The site itself uses approximately 692-694 MB under /var/www/vhosts; another vhost accounts for a much larger share of /var/www/vhosts usage.

This is an operational VPS risk, not a site-size issue.
