sudo useradd -M -r -s /bin/false monitoring
sudo mkdir -p /var/lib/prometheus/data && sudo chown -R monitoring:monitoring /var/lib/prometheus
systemctl restart node_exporter

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


# prom-node1
# Добавьте секцию scrape_configs для node_exporter

cat << EOF >> /opt/prometheus/prometheus.yml
  - job_name: 'node_exporter'
    scrape_interval: 5s
    static_configs:
      - targets: ['159.223.209.54:9100']
EOF

vi /etc/systemd/system/prometheus.service
--web.enable-admin-api

sudo systemctl daemon-reload &&\
systemctl restart prometheus

curl -X POST http://localhost:9090/api/v1/admin/tsdb/snapshot

# Prometheus на prom-node2:

cat << EOF >> /opt/prometheus/prometheus.yml
  - job_name: 'node_exporter'
    scrape_interval: 5s
    static_configs:
      - targets: ['159.223.209.54:9100']
EOF

vi /etc/systemd/system/prometheus.service
--web.enable-admin-api

sudo systemctl daemon-reload &&\
systemctl restart prometheus

curl -X POST http://localhost:9090/api/v1/admin/tsdb/snapshot