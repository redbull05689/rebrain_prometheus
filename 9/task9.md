
cat > /opt/prometheus/alerts.yml <<EOF
groups:
  - name: alert.rules
    rules:
      - alert: CompactionTimeTooLong
        expr: |
          histogram_quantile(0.95, rate(prometheus_tsdb_compaction_duration_seconds_bucket[5m])) >= 1
        for: 5m
        labels:
          severity: warning
          env: dev
        annotations:
          summary: "Comaction time on {{ $labels.instance }} equals {{ $value }}"
EOF