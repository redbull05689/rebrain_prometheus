sudo mkdir /opt/ssl/


openssl genrsa -out /opt/ssl/ca.key 4096
openssl req -new -x509 -days 720 -key /opt/ssl/ca.key -out /opt/ssl/server.crt

openssl genrsa -out /opt/ssl/server.key 4096
openssl req -new -key /opt/ssl/server.key -out /opt/ssl/server.csr

openssl x509 -req -days 365 -in /opt/ssl/server.csr -CA /opt/ssl/ca.crt -CAkey /opt/ssl/ca.key -set_serial 1 -out /opt/ssl/server.crt

apt update &&\
apt install nginx -y

cat > /etc/nginx/sites-available/prometheus <<EOF
server {
    listen 9191 ssl;
    server_name 134.209.90.84; # Замените на ваше доменное имя или IP-адрес

    ssl_certificate /opt/ssl/server.crt;
    ssl_certificate_key /opt/ssl/server.key;

    location / {
        proxy_pass http://127.0.0.1:9090;
        auth_basic "Restricted!";
        auth_basic_user_file .htpasswd;
    }

    auth_basic "Restricted Access";
    auth_basic_user_file /etc/nginx/.htpasswd;
}
EOF

sh -c "echo -n 'user:' >> /etc/nginx/.htpasswd" &&\
sh -c "openssl passwd -apr1 >> /etc/nginx/.htpasswd"

ln -s /etc/nginx/sites-available/prometheus /etc/nginx/sites-enabled/

systemctl restart nginx  &&\
systemctl status nginx