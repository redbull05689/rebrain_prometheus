sudo apt update &&\
sudo apt install redis-server -y


# экспортер Redis_exporter
wget https://github.com/oliver006/redis_exporter/releases/download/v1.54.0/redis_exporter-v1.54.0.linux-386.tar.gz &&\
tar -xvf redis_exporter-v1.54.0.linux-386.tar.gz &&\
sudo mkdir /opt/redis_exporter/ &&\
sudo mv redis_exporter-v1.54.0.linux-386/redis_exporter /opt/redis_exporter/


cat > /etc/systemd/system/redis_exporter.service << EOF
[Unit]
Description=Redis Exporter

[Service]
ExecStart=/opt/redis_exporter/redis_exporter 
Restart=always

[Install]
WantedBy=default.target
EOF

sudo systemctl daemon-reload &&\
sudo systemctl start redis_exporter &&\
sudo systemctl enable redis_exporter &&\
sudo systemctl status redis_exporter

vi /opt/prometheus/prometheus.yml
scrape_configs:
  - job_name: redis_exporter
    static_configs:
    - targets: ['localhost:9121']

sudo systemctl restart prometheus

apt install -y apt-transport-https &&\
apt install -y software-properties-common wget &&\
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add - &&\
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list


apt update &&\
apt install grafana

sudo systemctl start grafana-server &&\
sudo systemctl enable grafana-server &&\
sudo systemctl status grafana-server

s