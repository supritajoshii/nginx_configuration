# Nginx + Django Production Configuration

## Directory layout

Copy the matching files into:

    /etc/nginx/nginx.conf
    /etc/nginx/conf.d/upstreams.conf
    /etc/nginx/conf.d/cache.conf
    /etc/nginx/conf.d/rate-limit.conf
    /etc/nginx/snippets/proxy-params.conf
    /etc/nginx/snippets/websocket-params.conf
    /etc/nginx/snippets/ssl.conf
    /etc/nginx/sites-available/myapp.conf

Enable the site:

    sudo ln -s /etc/nginx/sites-available/myapp.conf /etc/nginx/sites-enabled/myapp.conf

Create cache directory if needed:

    sudo mkdir -p /var/cache/nginx
    sudo chown -R www-data:www-data /var/cache/nginx

Check configuration:

    sudo nginx -t

Reload:

    sudo systemctl reload nginx

## TLS

Install Certbot and obtain a certificate:

    sudo apt install certbot python3-certbot-nginx
    sudo certbot --nginx -d example.com -d www.example.com

Test renewal:

    sudo certbot renew --dry-run

## Django

Collect static files:

    python manage.py collectstatic

Make sure Gunicorn is running on:

    127.0.0.1:8000

## Cache

The example caches only:

    /api/public/

Do not blindly cache authenticated or personalized responses.

Useful response header:

    X-Cache-Status: MISS
    X-Cache-Status: HIT
    X-Cache-Status: BYPASS
    X-Cache-Status: EXPIRED
    X-Cache-Status: STALE
    X-Cache-Status: UPDATING

Test:

    curl -I https://example.com/api/public/products/

First request should commonly show MISS; repeated requests can show HIT
while the cached entry remains valid.

## Important

Replace:

    example.com
    /var/www/myapp/
    myproject
    certificate paths

with your actual deployment values.

Review CSP, HSTS, cache rules, upload limits, timeouts, worker counts,
and rate limits for your application before production use.
