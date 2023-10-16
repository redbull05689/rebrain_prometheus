sudo mkdir -p /etc/prom-targets

sudo su -

cat > /etc/prom-targets/dd.json <<EOF
[
  {
    "targets": ["192.168.0.100:9100", "192.168.0.200:9100"],
    "labels": {
      "env": "dev"
    }
  }
]
EOF

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

vim /etc/prometheus/prometheus.yml

scrape_configs:
  - job_name: 'data'
    file_sd_configs:
      - files:
        - /etc/prom-targets/*.json
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: localhost:9090  # Порт, на котором слушает Prometheus
