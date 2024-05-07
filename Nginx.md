# Redirect HTTP to HTTPS
```nginx.conf
server {
    listen 80;
    server_name localhost;
    return 301 https://$host$request_uri;
}

# HTTPS server with enhancements

server {
    listen 443 ssl http2;
    server_name localhost;

    ssl_certificate /path/to/your/fullchain.pem;
    ssl_certificate_key /path/to/your/privatekey.pem;

    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    # HTTP/2 and SSL
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;

    # Security Headers
    add_header X-Frame-Options "DENY";
    add_header X-Content-Type-Options "nosniff";
    add_header X-XSS-Protection "1; mode=block";
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline'; object-src 'none';";
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";

    # Compression with gzip
    gzip on;
    gzip_disable "msie6";
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_buffers 16 8k;
    gzip_http_version 1.1;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    # Brotli Compression
    brotli on;
    brotli_comp_level 6;
    brotli_static on;
    brotli_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    # Serve React static files
    location / {
        root /path/to/your/react/dist;
        try_files $uri /index.html;

        # Cache settings for static files
        location ~* \.(css|js|gif|jpeg|jpg|png|svg|woff|woff2|otf|ttf|eot|ico)$ {
            expires 5m;
            add_header Cache-Control "public, no-transform";
        }
    }

    # Proxy API requests to Django
    location /api/ {
        limit_req zone=api_limit burst=20 nodelay; # Applying rate limiting
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_pass http://127.0.0.1:8000;
    }

    # Custom Error Pages
    error_page 404 /404.html;
    location = /404.html {
        root /path/to/your/react/build;
        internal;
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /path/to/your/react/build;
        internal;
    }

    # Logging
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log warn;
}

# Rate limiting for API requests

limit_req_zone $binary_remote_addr zone=api_limit:10m rate=5r/s;```
