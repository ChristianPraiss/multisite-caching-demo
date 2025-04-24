Here's the nginx rule we use in our project.

upstream octane_upstream {
  server 127.0.0.1:8000;
  keepalive 64;
}

server {
    listen 80;
    server_name multisite-caching-demo.test web2.test;
    root /var/www/html/public;

    # Handle static files
    location / {
        try_files /static/${host}${uri}_$args.html $uri @octane;
    }

    # Proxy to Octane
    location @octane {
        proxy_pass http://octane_upstream;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass_request_headers      on;
    }
}
