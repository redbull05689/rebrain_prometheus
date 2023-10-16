vi /opt/prometheus/prometheus.yml
```
rule_files:
  - /opt/prometheus/alerts.yml

```

vi /opt/prometheus/alerts.yml
```
groups:
  - name: example
    rules:
      - alert: InstanceDown
        expr: up == 0
        for: 1m
        labels:
          severity: critical
        annotations:
          description: "Instance is down: {{ $labels.instance }}"

```

mkdir /opt/alertmanager &&\
wget https://github.com/prometheus/alertmanager/releases/download/v0.22.2/alertmanager-0.22.2.linux-amd64.tar.gz &&\
tar -xzvf alertmanager-0.22.2.linux-amd64.tar.gz &&\
cp alertmanager-0.22.2.linux-amd64/alertmanager* /opt/alertmanager/

cat > /etc/systemd/system/alertmanager.service <<EOF
[Unit]
Description=Alertmanager

[Service]
ExecStart=/opt/alertmanager/alertmanager --config.file=/opt/alertmanager/alertmanager.yml
Restart=always

[Install]
WantedBy=multi-user.target
EOF

vi /opt/alertmanager/alertmanager.yml
```
global:
  resolve_timeout: 5m

route:
  receiver: 'email'
  group_wait: 10s
  group_interval: 5m
  repeat_interval: 3h

receivers:
  - name: 'email'
    email_configs:
      - to: 'redbull05689@mail.com'
        from: 'redbull05689@gmail.com'
        smarthost: 'smtp.gmail.com:587'
        auth_username: 'логин'
        auth_password: 'пароль'
```

systemctl daemon-reload &&\
systemctl start alertmanager &&\
systemctl enable alertmanager &&\
systemctl status alertmanager


vi /opt/prometheus/prometheus.yml
```
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          - "localhost:9093"
```

systemctl restart prometheus &&\
systemctl restart alertmanager &&\
systemctl status prometheus &&\
systemctl status alertmanager