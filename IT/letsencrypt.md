# 获取 https 证书及自动更新

Ref: [https://certbot.eff.org/#ubuntuxenial-nginx](https://certbot.eff.org/#ubuntuxenial-nginx)

### 使用 certbot 获取 https 证书

```
sudo apt-get install letsencrypt
letsencrypt certonly --webroot -w /var/www/example -d example.com -d www.example.com
```

### 测试自动更新

    letsencrypt renew --dry-run --agree-tos


### 将自动更新任务写到 crontab

    sudo crontab -e

每周一凌晨2点自动更新证书：

    30 2 * * 1 /usr/bin/letsencrypt renew >> /var/log/le-renew.log
    35 2 * * 1 /bin/systemctl reload nginx

# 在 Nginx 启用 https

REF1: [How To Secure Nginx with Let's Encrypt on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04)

REF2: [How To Set Up Nginx with HTTP/2 Support on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-set-up-nginx-with-http-2-support-on-ubuntu-16-04)

### 生成更安全的 Diffie-Hellman Group

    sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048

### 定义 ssl 参数

    sudo nano -w /etc/nginx/snippets/ssl-params.conf

内容为：

```
# from https://cipherli.st/
# and https://raymii.org/s/tutorials/Strong_SSL_Security_On_nginx.html

ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
ssl_prefer_server_ciphers on;
# ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH";
ssl_ciphers EECDH+CHACHA20:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
ssl_ecdh_curve secp384r1;
ssl_session_cache shared:SSL:10m;
ssl_session_tickets off;
ssl_stapling on;
ssl_stapling_verify on;
resolver 8.8.8.8 8.8.4.4 valid=300s;
resolver_timeout 5s;
# Disable preloading HSTS for now.  You can use the commented out header line that includes
# the "preload" directive if you understand the implications.
#add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload";
add_header Strict-Transport-Security "max-age=63072000; includeSubdomains";
add_header X-Frame-Options DENY;
add_header X-Content-Type-Options nosniff;

ssl_dhparam /etc/ssl/certs/dhparam.pem;
```

### 配置 nginx 启用 https

    sudo nano -w /etc/nginx/sites-available/default

大致内容如下：

```
server {
    listen 80;
    listen [::]:80;
    server_name example.com www.example.com;
    return 301 https://$server_name$request_uri;
}

server {

    # SSL configuration

    listen 443 ssl http2;
    listen [::]:443 ssl http2;

    server_name example.com www.example.com;
    
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

    include snippets/ssl-params.conf;

    . . .
```