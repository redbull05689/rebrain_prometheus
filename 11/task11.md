### Node1
sudo su -

mkdir /opt/node_exporter && \
cd /opt/node_exporter && \
sudo wget https://github.com/prometheus/node_exporter/releases/download/v1.2.2/node_exporter-1.2.2.linux-amd64.tar.gz && \
tar -xzf node_exporter-1.2.2.linux-amd64.tar.gz && \
mv node_exporter-1.2.2.linux-amd64/* . && \
rmdir node_exporter-1.2.2.linux-amd64



cat > /etc/systemd/system/node_exporter.service <<EOF
[Unit]
Description=Node Exporter

[Service]
ExecStart=/opt/node_exporter/node_exporter
Restart=always

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload &&\
systemctl start node_exporter &&\
systemctl enable node_exporter

cat > /opt/prometheus/rules.yml <<EOF
groups:
  - name: example
    rules:
      - record: job:total_avail_disk_space
        expr: sum(node_filesystem_avail_bytes) by (job)

EOF

vi /opt/prometheus/prometheus.yml
```
global:
  scrape_interval:     15s
  evaluation_interval: 15s
  external_labels:
    type: external
....
scrape_configs:
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']
    relabel_configs:
      - source_labels: ['__name__']
        regex: '(.+)'
        target_label: 'type'
        replacement: 'external'
....

rule_files:
  - "/opt/prometheus/rules.yml"
```
vi /opt/prometheus/rules.yml
```
groups:
  - name: example
    rules:
      - record: job:total_avail_disk_space
        expr: sum(node_filesystem_avail_bytes) by (job)
```

systemctl daemon-reload &&\
systemctl start node_exporter &&\
systemctl enable node_exporter &&\
systemctl status node_exporter

systemctl restart prometheus &&\
systemctl status prometheus

### Node2

vi /opt/prometheus/prometheus.yml
```
scrape_configs:
  - job_name: 'total_avail_disk_space'
    honor_labels: true
    metrics_path: '/federate'
    params:
      match[]:
        - '{__name__=~"^job:.*"}'
    static_configs:
      - targets: 
        - node1:9090 # заменить на адрес

```

systemctl restart prometheus &&\
systemctl status prometheus