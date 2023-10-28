# Prometheus conf
```
scrape_configs:
  - job_name: 'cadvisor'
    static_configs:
      - targets: ['localhost:8080']
  - job_name: 'mysqld_exporter'
    static_configs:
      - targets: ['localhost:9104']
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']
```

# cadvisor logs
```

```

# Mysqld logs
```
docker logs 593963dc4d0e
ts=2023-10-27T07:02:28.151Z caller=mysqld_exporter.go:220 level=info msg="Starting mysqld_exporter" version="(version=0.15.0, branch=HEAD, revision=6ca2a42f97f3403c7788ff4f374430aa267a6b6b)"
ts=2023-10-27T07:02:28.151Z caller=mysqld_exporter.go:221 level=info msg="Build context" build_context="(go=go1.20.5, platform=linux/amd64, user=root@c4fca471a5b1, date=20230624-04:09:04, tags=netgo)"
ts=2023-10-27T07:02:28.151Z caller=mysqld_exporter.go:233 level=info msg="Scraper enabled" scraper=global_status
ts=2023-10-27T07:02:28.151Z caller=mysqld_exporter.go:233 level=info msg="Scraper enabled" scraper=global_variables
ts=2023-10-27T07:02:28.151Z caller=mysqld_exporter.go:233 level=info msg="Scraper enabled" scraper=slave_status
ts=2023-10-27T07:02:28.151Z caller=mysqld_exporter.go:233 level=info msg="Scraper enabled" scraper=info_schema.innodb_cmp
ts=2023-10-27T07:02:28.151Z caller=mysqld_exporter.go:233 level=info msg="Scraper enabled" scraper=info_schema.innodb_cmpmem
ts=2023-10-27T07:02:28.151Z caller=mysqld_exporter.go:233 level=info msg="Scraper enabled" scraper=info_schema.query_response_time
ts=2023-10-27T07:02:28.153Z caller=tls_config.go:274 level=info msg="Listening on" address=[::]:9104
ts=2023-10-27T07:02:28.153Z caller=tls_config.go:277 level=info msg="TLS is disabled." http2=false address=[::]:9104
```

# Node_exporter logs
```
docker logs e2236a70b7e3
ts=2023-10-27T06:35:39.836Z caller=node_exporter.go:180 level=info msg="Starting node_exporter" version="(version=1.6.1, branch=HEAD, revision=4a1b77600c1873a8233f3ffb55afcedbb63b8d84)"
ts=2023-10-27T06:35:39.836Z caller=node_exporter.go:181 level=info msg="Build context" build_context="(go=go1.20.6, platform=linux/amd64, user=root@586879db11e5, date=20230717-12:10:52, tags=netgo osusergo static_build)"
ts=2023-10-27T06:35:39.838Z caller=filesystem_common.go:111 level=info collector=filesystem msg="Parsed flag --collector.filesystem.mount-points-exclude" flag=^/(dev|proc|run/credentials/.+|sys|var/lib/docker/.+|var/lib/containers/storage/.+)($|/)
ts=2023-10-27T06:35:39.838Z caller=filesystem_common.go:113 level=info collector=filesystem msg="Parsed flag --collector.filesystem.fs-types-exclude" flag=^(autofs|binfmt_misc|bpf|cgroup2?|configfs|debugfs|devpts|devtmpfs|fusectl|hugetlbfs|iso9660|mqueue|nsfs|overlay|proc|procfs|pstore|rpc_pipefs|securityfs|selinuxfs|squashfs|sysfs|tracefs)$
ts=2023-10-27T06:35:39.839Z caller=diskstats_common.go:111 level=info collector=diskstats msg="Parsed flag --collector.diskstats.device-exclude" flag=^(ram|loop|fd|(h|s|v|xv)d[a-z]|nvme\d+n\d+p)\d+$
ts=2023-10-27T06:35:39.839Z caller=diskstats_linux.go:265 level=error collector=diskstats msg="Failed to open directory, disabling udev device properties" path=/run/udev/data
ts=2023-10-27T06:35:39.840Z caller=node_exporter.go:110 level=info msg="Enabled collectors"
ts=2023-10-27T06:35:39.840Z caller=node_exporter.go:117 level=info collector=arp
ts=2023-10-27T06:35:39.840Z caller=node_exporter.go:117 level=info collector=bcache
ts=2023-10-27T06:35:39.841Z caller=node_exporter.go:117 level=info collector=bonding
ts=2023-10-27T06:35:39.841Z caller=node_exporter.go:117 level=info collector=btrfs
ts=2023-10-27T06:35:39.841Z caller=node_exporter.go:117 level=info collector=conntrack
ts=2023-10-27T06:35:39.841Z caller=node_exporter.go:117 level=info collector=cpu
ts=2023-10-27T06:35:39.841Z caller=node_exporter.go:117 level=info collector=cpufreq
ts=2023-10-27T06:35:39.841Z caller=node_exporter.go:117 level=info collector=diskstats
ts=2023-10-27T06:35:39.842Z caller=node_exporter.go:117 level=info collector=dmi
ts=2023-10-27T06:35:39.842Z caller=node_exporter.go:117 level=info collector=edac
ts=2023-10-27T06:35:39.842Z caller=node_exporter.go:117 level=info collector=entropy
ts=2023-10-27T06:35:39.842Z caller=node_exporter.go:117 level=info collector=fibrechannel
ts=2023-10-27T06:35:39.842Z caller=node_exporter.go:117 level=info collector=filefd
ts=2023-10-27T06:35:39.842Z caller=node_exporter.go:117 level=info collector=filesystem
ts=2023-10-27T06:35:39.842Z caller=node_exporter.go:117 level=info collector=hwmon
ts=2023-10-27T06:35:39.843Z caller=node_exporter.go:117 level=info collector=infiniband
ts=2023-10-27T06:35:39.843Z caller=node_exporter.go:117 level=info collector=ipvs
ts=2023-10-27T06:35:39.843Z caller=node_exporter.go:117 level=info collector=loadavg
ts=2023-10-27T06:35:39.843Z caller=node_exporter.go:117 level=info collector=mdadm
ts=2023-10-27T06:35:39.843Z caller=node_exporter.go:117 level=info collector=meminfo
ts=2023-10-27T06:35:39.843Z caller=node_exporter.go:117 level=info collector=netclass
ts=2023-10-27T06:35:39.844Z caller=node_exporter.go:117 level=info collector=netdev
ts=2023-10-27T06:35:39.844Z caller=node_exporter.go:117 level=info collector=netstat
ts=2023-10-27T06:35:39.844Z caller=node_exporter.go:117 level=info collector=nfs
ts=2023-10-27T06:35:39.844Z caller=node_exporter.go:117 level=info collector=nfsd
ts=2023-10-27T06:35:39.844Z caller=node_exporter.go:117 level=info collector=nvme
ts=2023-10-27T06:35:39.844Z caller=node_exporter.go:117 level=info collector=os
ts=2023-10-27T06:35:39.844Z caller=node_exporter.go:117 level=info collector=powersupplyclass
ts=2023-10-27T06:35:39.845Z caller=node_exporter.go:117 level=info collector=pressure
ts=2023-10-27T06:35:39.845Z caller=node_exporter.go:117 level=info collector=rapl
ts=2023-10-27T06:35:39.845Z caller=node_exporter.go:117 level=info collector=schedstat
ts=2023-10-27T06:35:39.845Z caller=node_exporter.go:117 level=info collector=selinux
ts=2023-10-27T06:35:39.845Z caller=node_exporter.go:117 level=info collector=sockstat
ts=2023-10-27T06:35:39.845Z caller=node_exporter.go:117 level=info collector=softnet
ts=2023-10-27T06:35:39.845Z caller=node_exporter.go:117 level=info collector=stat
ts=2023-10-27T06:35:39.845Z caller=node_exporter.go:117 level=info collector=tapestats
ts=2023-10-27T06:35:39.845Z caller=node_exporter.go:117 level=info collector=textfile
ts=2023-10-27T06:35:39.845Z caller=node_exporter.go:117 level=info collector=thermal_zone
ts=2023-10-27T06:35:39.845Z caller=node_exporter.go:117 level=info collector=time
ts=2023-10-27T06:35:39.845Z caller=node_exporter.go:117 level=info collector=timex
ts=2023-10-27T06:35:39.845Z caller=node_exporter.go:117 level=info collector=udp_queues
ts=2023-10-27T06:35:39.845Z caller=node_exporter.go:117 level=info collector=uname
ts=2023-10-27T06:35:39.845Z caller=node_exporter.go:117 level=info collector=vmstat
ts=2023-10-27T06:35:39.845Z caller=node_exporter.go:117 level=info collector=xfs
ts=2023-10-27T06:35:39.845Z caller=node_exporter.go:117 level=info collector=zfs
ts=2023-10-27T06:35:39.847Z caller=tls_config.go:274 level=info msg="Listening on" address=[::]:9100
ts=2023-10-27T06:35:39.847Z caller=tls_config.go:277 level=info msg="TLS is disabled." http2=false address=[::]:9100
```