# static-site-setup
A quick setup for static sites.

```
sudo apt update
sudo apt install nginx
```

nginx conf in /etc/nginx/sites-available/domain.tld.conf
```
server {
  listen 80 default_server;
  listen [::]:80 default_server;
  root /var/www/jgefroh.com;
  index index.html;
  server_name jgefroh.com www.jgefroh.com;
  location / {
    try_files $uri $uri/ =404;
  }
}
```

symlink to enabled
```
cd sites-enabled
sudo ln -s ../sites-available/domain.tld.conf .
ls -l
```

Restart nginx
`systemctl restart nginx`

Install certbot
`snap install --classic certbot`

Run certbot
`certbot --nginx certonly`

Add certbot to cron
`sudo crontab -e`
```
17 7 * * * certbot renew --post-hook "systemctl reload nginx"
```

SSL config for nginx

```
server {
   # ...previous content here
   ssl on;
   ssl_certificate /etc/letsencrypt/live/jgefroh.com/fullchain.pem;
   ssl_certificate_key /etc/letsencrypt/live/jgefroh.com/privkey.pem;
```

```
server {
   listen 443 default_server;
   listen [::]:443 default_server;
   #... all other content
}
```

To redirect HTTP to HTTPS, add another server block below the current one, with
```
server {
    listen 0.0.0.0:80;
    server_name jgefroh.com www.jgefroh.com;
    rewrite ^ https://$host$request_uri? permanent;
}
```

Restart nginx, `sudo systemctl restart nginx`, and you're good to go!
