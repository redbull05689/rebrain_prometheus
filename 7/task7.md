# Сначала скачайте Node Exporter
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-386.tar.gz &&\
tar -xvf node_exporter-1.6.1.linux-386.tar.gz &&\
sudo mkdir /opt/node_exporter/ &&\
sudo mv node_exporter-1.6.1.linux-386/node_exporter /opt/node_exporter/ 


sudo cat > /etc/systemd/system/node_exporter.service <<EOF
[Unit]
Description=Node Exporter

[Service]
ExecStart=/opt/node_exporter/node_exporter
Restart=always

[Install]
WantedBy=default.target
EOF


sudo systemctl daemon-reload &&\
sudo systemctl start node_exporter &&\
sudo systemctl enable node_exporter &&\
sudo systemctl status node_exporter 

cd /opt

sudo wget https://github.com/prometheus/prometheus/releases/download/v2.30.3/prometheus-2.30.3.linux-amd64.tar.gz && \
sudo tar -xzf prometheus-2.30.3.linux-amd64.tar.gz && \
sudo mv prometheus-2.30.3.linux-amd64 prometheus && \
sudo rm prometheus-2.30.3.linux-amd64.tar.gz

# Создайте директорию для базы данных Prometheus:

sudo useradd -M -r -s /bin/false monitoring &&\
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
systemctl start prometheus && \
systemctl status prometheus


vi /opt/prometheus/prometheus.yml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']

systemctl restart prometheus &&\
systemctl status prometheus




./promtool query instant http://localhost:9090/ 'sum(irate(node_cpu_seconds_total{mode="user"}[5m])) * 100' > /tmp/query1.txt


./promtool query instant http://localhost:9090/ '(1 - node_filesystem_avail_bytes{mountpoint="/"} / node_filesystem_size_bytes{mountpoint="/"}) * 100' > /tmp/query2.txt
