apt update &&\ 
apt install postgresql-12 -y

systemctl status postgresql

sudo  -u postgres psql
CREATE USER prom WITH PASSWORD 'password';

ALTER USER prom WITH SUPERUSER;

CREATE DATABASE timescale OWNER prom;


curl -s https://packagecloud.io/install/repositories/timescale/timescaledb/script.deb.sh | sudo bash
apt-get update &&\
apt-get install timescaledb-2-postgresql-12 -y


vi /etc/postgresql/12/main/postgresql.conf
    shared_preload_libraries = 'timescaledb'

systemctl restart postgresql &&\
systemctl status postgresql


### Install promscale
 wget https://github.com/timescale/promscale/releases/download/0.4.1/promscale_0.4.1_Linux_x86_64




vi /opt/prometheus/prometheus.yml
remote_write:
  - url: "http://localhost:9201/write"

/usr/bin/promscale -db.uri postgres://prom:password@localhost:5432/timescale?sslmode=disable

systemctl restart prometheus &&\
systemctl status prometheus 


