
В этом задании вам будет необходимо запустить pushgateway, создать несколько метрик внутри него и добавить его мониторинг в prometheus. На подготовленной виртуальной машине уже установлен Prometheus.

Критерии оценки выполнения задания:

Установите pushgateway на подготовленной виртуальной машине.
1.1. Исполняемый файл pushgateway должен находиться внутри отдельной директории /opt/pushgateway.

1.2. Pushgateway должен быть запущен на порту 9991.

1.3. Pushgateway должен быть запущен как сервис c помощью systemd и быть описан в файле /etc/systemd/system/pushgateway.service.

Отправьте в pushgateway метрики разделенные две группы:
группа job=test1,instance=app1 с метрикой time_run 3.14
группа job=test1,instance=app2 с метрикой time_run 6.18
тип метрик должен быть gauge
Настройте Prometheus:
3.1. Prometheus должен собирать все метрики c pushgateway без перезаписи тегов job и instance.

sudo mkdir /opt/pushgateway/ &&\
sudo wget https://github.com/prometheus/pushgateway/releases/download/v1.4.3/pushgateway-1.4.3.linux-amd64.tar.gz -O /opt/pushgateway/pushgateway-1.4.3.linux-amd64.tar.gz &&\
sudo tar -zxvf pushgateway-1.4.3.linux-amd64.tar.gz

useradd --no-create-home --shell /bin/false pushgateway && /
chown -R pushgateway:pushgateway /opt/pushgateway/

cat > /etc/systemd/system/pushgateway.service <<EOF
[Unit]
Description=Pushgateway Service
After=network.target

[Service]
User=pushgateway
Group=pushgateway
Type=simple
ExecStart=/opt/pushgateway/pushgateway \
    --web.listen-address=":9991" \
    --web.telemetry-path="/metrics" \
    --persistence.file="/tmp/metric.store" \
    --persistence.interval=5m \
    --log.level="info" \
    --log.format="json"
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload &&\
systemctl enable pushgateway &&\
systemctl start pushgateway

systemctl restart prometheus


vi prometheus.yml
  - job_name: 'pushgateway'
    static_configs:
    - targets: ['pushgateway:9991']
    honor_labels: true


cat <<EOF | curl --data-binary @- http://localhost:9991/metrics/job/test1/instance/app1
time_run{type="gauge"} 3.14
EOF

cat <<EOF | curl --data-binary @- http://localhost:9991/metrics/job/test1/instance/app2
time_run{type="gauge"} 6.18
EOF