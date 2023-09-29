sudo useradd -M -r -s /bin/false monitoring

sudo su -

cd /opt

sudo wget https://github.com/prometheus/prometheus/releases/download/v2.30.3/prometheus-2.30.3.linux-amd64.tar.gz && \
sudo tar -xzf prometheus-2.30.3.linux-amd64.tar.gz && \
sudo mv prometheus-2.30.3.linux-amd64 prometheus && \
sudo rm prometheus-2.30.3.linux-amd64.tar.gz

# Создайте директорию для базы данных Prometheus:

sudo mkdir -p /var/lib/prometheus/data && \
sudo chown -R monitoring:monitoring /var/lib/prometheus

mv  /opt/prometheus/prometheus.yml /opt/prometheus/prometheus.yml.example
cat > /opt/prometheus/prometheus.yml << EOF
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
EOF

# Создайте файл systemd для Prometheus

cat > /etc/systemd/system/prometheus.service << EOF
[Unit]
Description=Prometheus Monitoring
Documentation=https://prometheus.io/docs/introduction/overview/
After=network-online.target

[Service]
User=monitoring
ExecStart=/opt/prometheus/prometheus --config.file=/opt/prometheus/prometheus.yml --storage.tsdb.path=/var/lib/prometheus/data
ExecReload=/bin/kill -HUP $MAINPID
TimeoutStopSec=30s
Restart=always

[Install]
WantedBy=multi-user.target

EOF


systemctl daemon-reload && \
systemctl enable prometheus && \
systemctl start prometheus

systemctl status prometheus

# Скачайте node_exporter и разместите его в /opt/node_exporter

mkdir -p /opt/node_exporter && \
cd /opt/node_exporter && \
sudo wget https://github.com/prometheus/node_exporter/releases/download/v1.2.2/node_exporter-1.2.2.linux-amd64.tar.gz && \
tar -xzf node_exporter-1.2.2.linux-amd64.tar.gz && \
mv node_exporter-1.2.2.linux-amd64/* . && \
rmdir node_exporter-1.2.2.linux-amd64

# Создайте файл systemd для node_exporter

cat > /etc/systemd/system/node_exporter.service << EOF
[Unit]
Description=Node Exporter
Documentation=https://github.com/prometheus/node_exporter
After=network.target

[Service]
User=monitoring
ExecStart=/opt/node_exporter/node_exporter --collector.interrupts
Restart=always

[Install]
WantedBy=multi-user.target
EOF


systemctl daemon-reload && \
systemctl enable node_exporter && \
systemctl start node_exporter

# Добавьте секцию scrape_configs для node_exporter

cat << EOF >> /opt/prometheus/prometheus.yml
  - job_name: 'node_exporter'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9100']
EOF

systemctl restart prometheus