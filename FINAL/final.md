# node1
apt-get update  &\
apt-get install -y docker.io


curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose &\
chmod +x /usr/local/bin/docker-compose


mkdir ~/db_data &\
mkdir ~/wordpress_data

cd ~ 

cat > docker-compose.yml <<EOF
version: "3.8"

services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: YOUR_ROOT_DB_PASSWORD
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: YOUR_DB_PASSWORD

  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - wordpress_data:/var/www/html
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: YOUR_DB_PASSWORD
      WORDPRESS_DB_NAME: wordpress

  mysqld-exporter:
    image: quay.io/prometheus/mysqld-exporter
    container_name: mysqld-exporter
    ports:
      - "9104:9104"
    restart: always
    command:
     - "--mysqld.username=root:YOUR_ROOT_DB_PASSWORD"
     - "--mysqld.address=db:3306"	

volumes:
  db_data: {}
  wordpress_data: {}
EOF



docker-compose up -d

docker run -d --name=cadvisor --net=host --pid=host --privileged --restart=always -v /:/rootfs:ro -v /var/run:/var/run:rw -v /sys:/sys:ro -v /var/lib/docker/:/var/lib/docker:ro google/cadvisor:latest --log_cadvisor_usage=true



docker run -d --net="host" --pid="host" --restart=always --name=node_exporter -v "/:/host:ro,rslave" quay.io/prometheus/node-exporter


# Prometheus

apt-get update \&
apt-get install -y docker.io

cat > /home/user/prometheus.yml <<EOF
scrape_configs:
  - job_name: 'cadvisor'
    static_configs:
      - targets: ['165.22.200.10:8080']
  - job_name: 'mysqld_exporter'
    static_configs:
      - targets: ['165.22.200.10:9104']
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['165.22.200.10:9100']

EOF

docker run -d -p 9090:9090 --name=prometheus -v /home/user/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus

docker run -d -p 3000:3000 --name=grafana grafana/grafana
