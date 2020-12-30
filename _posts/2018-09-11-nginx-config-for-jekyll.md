---
title: Nginx config for a Jekyll site
date: 2018-09-11 21:32:12 +0800
categories: [Deployment]
tags: [deployment, nginx, jekyll]
---

## Example Nginx config

{% highlight nginx %}
server {
    listen 443 ssl;
    server_name blog.example.com;
    root /opt/blog.example.com;

    gzip_static on;
    gzip_vary on;

    error_page 404 /404/index.html;
    error_page 403 =404 /404/index.html;

    add_header X-Frame-Options sameorigin always;
    add_header X-Content-Type-Options nosniff always;
    add_header X-XSS-Protection "1; mode=block" always;

    location ~ ^(?<path>/.*/)(?<file>[^/]+)/$ {
      try_files $uri $path$file.html $uri/ =404;
    }

    location /_assets/ {
      expires max;
      access_log off;
    }
}
{% endhighlight %}