###  MySQL должен быть установлен из стандартного репозитория с помощью менеджера пакетов apt (пакет mysql-server).

sudo apt update && sudo apt install mysql-server -y

### В MySQL должен быть создан отдельный пользователь exporter с паролем Pa$$W0rd.

sudo mysql -u root -p

CREATE USER 'exporter'@'localhost' IDENTIFIED BY 'Pa$$W0rd';

GRANT ALL PRIVILEGES ON *.* TO 'exporter'@'localhost';

FLUSH PRIVILEGES;
EXIT;


### Установка mysqld_exporter
### Загрузите mysqld_exporter из официального репозитория Prometheus с помощью wget в /opt/mysqld_exporter

sudo apt install wget && \
sudo mkdir /opt/mysqld_exporter && \
cd /opt/mysqld_exporter && \
sudo wget https://github.com/prometheus/mysqld_exporter/releases/download/v0.13.0/mysqld_exporter-0.13.0.linux-amd64.tar.gz && \
sudo tar xvf mysqld_exporter-0.13.0.linux-amd64.tar.gz && \
sudo mv mysqld_exporter-0.13.0.linux-amd64/* /opt/mysqld_exporter/

sudo useradd -M -r -s /bin/false exporter && \
sudo chown -R exporter:exporter /opt/mysqld_exporter

sudo chmod +x /opt/mysqld_exporter/mysqld_exporter 

sudo cat > /opt/mysqld_exporter/mysqld_exporter.yml << EOF
[client]
user=exporter
password=Pa$$W0rd
host=127.0.0.1
EOF

### Создайте файл службы systemd для mysqld_exporter в /etc/systemd/system/mysqld_exporter.service:

sudo cat > /etc/systemd/system/mysqld_exporter.service << EOF
[Unit]
Description=Prometheus MySQL Exporter
After=network.target

[Service]
User=exporter
ExecStart=/opt/mysqld_exporter/mysqld_exporter \
  --web.listen-address=:9111 \
  --config.my-cnf=/opt/mysqld_exporter/mysqld_exporter.yml
Restart=always

[Install]
WantedBy=multi-user.target
EOF


### Добавьте секцию scrape_configs для node_exporter

vi /opt/prometheus/prometheus.yml
  - job_name: 'mysql_exporter'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9111']

systemctl daemon-reload 

sudo systemctl restart prometheus

sudo systemctl start mysqld_exporter && \
sudo systemctl enable mysqld_exporter
sudo systemctl status mysqld_exporter

